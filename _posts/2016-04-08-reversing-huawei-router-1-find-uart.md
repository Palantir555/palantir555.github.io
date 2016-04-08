---
layout: post
title: Practical Reverse Engineering Part 1 - Hunting for Debug Ports
tags:
- reverse engineering
- embedded
- security
---

In this series of posts we're gonna go through the process of Reverse Engineering
a router. More specifically, a Huawei HG533.

![Huawei HG533](https://i.imgur.com/UsxvPMo.jpg)

At the earliest stages, this is the most basic kind of reverse engineering.
We're simple looking for a serial port that the engineers who designed the device
left in the board for debug and -potentially- technical support purposes.

Even though I'll be explaining the process using a router, it can be applied to
tons of household embedded systems. From printers to IP cameras, if
it's mildly complex it's quite likely to be running some form of linux. It will
also probably have hidden debug ports like the ones we're gonna be looking for
in this post.

## Finding the Serial Port

Most UART ports I've found in commercial products are between 4 and 6 pins,
usually neatly aligned and sometimes marked in the PCB's silk mask somehow.
They're not supposed to be used by users, so they usually have no pins or connector
attached.

After taking a quick look at the board, 2 sets of unused pads call my atention
(they were unused before I soldered those pins in the picture, anyway):

![Pic of the 2 Potential UART Ports](https://i.imgur.com/5gJUa8R.jpg)

This device seems to have 2 different serial ports to communicate with
2 different Integrated Circuits (ICs). Based on the location on the board and
following their traces we can figure out which one is connected to the main IC.
That's the most likely one to have juicy data.

In this case we're simply gonna try connecting to both of them and find out what
each of them has to offer.

## Identifying Useless Pins

The first thing you want to do now that you've found what you think to be UART
ports, is see if any of those pins are actually not connected to anything.
A neat way I've found to do so is to flash a bright light at the bottom of the
PCB and look at it from directly up front. This is what that looks like:

![2nd Serial Port - No Headers](https://i.imgur.com/g0REmPG.jpg)

We can see if any of the layers of the PCB is making contact with the solder
blob in the middle of the pad.

1. **Connected** to something (we can see a trace "at 2 o'clock")
2. NOT CONNECTED
3. 100% connected to a layer or more. This usually means the pin is not
being used for anything. On paper, it could be connected to a GND or Vcc
plane, but that's not common practice in the industry
4. Connections at all sides. This is almost definitely the **GND** pin. There's
no reason for a data pin in a debug port to be connected to 4 different traces,
but the pad being surrounded by a ground plane would explain those connections
5. **Connected** to something

## Soldering Pins for Easy Access to the Lines

In the picture above we can see both serial ports.

The pads in these ports are through-hole, but the holes themselves are filled in
with blobs of very hard, very high-melting point solder.

I tried soldering the pins over the pads, but the solder they used is not easy
to work with. For the 2nd serial port I decided to drill through the solder blobs
with a Dremel and a needle bit. That way we can pass the pins through the holes
and solder them properly on the back of the PCB. It worked like a charm.

![Use a Dremel to Drill Through the Solder Blobs](https://i.imgur.com/a8p40yt.jpg)

## Identifying the Pinout

So we've got 2 connectors with only 3 useful pins each. We still haven't verified
the ports are operative or identified the serial protocol used by the device, but
the number and arrangement of pins hint at UART.

Let's review the UART protocol. There are 6 pin types in the spec:

- Tx  [Transmitting Pin. Connects to our Rx]
- Rx  [Receiving Pin. Connects to our Tx]
- GND [Ground. Connects to our GND]
- Vcc [The board's power line. Usually 3.3V or 5V. DO NOT CONNECT]
- CTS [Typically unused]
- DTR [Typically unused]

We also know that according to the Standard, Tx and Rx are pulled up (set to 1)
by default. The Transmitter of the line (Tx) is in charge of pulling it up,
which means if it's not connected the line's voltage will float.

So lets compile what we know and get to some conclusions:

1. Only 3 pins in each header are likely to be connected to anything. **Those
must be Tx, Rx and GND**
2. One pin looks a lot like GND (connected to a plane on 4 sides)
3. One of them -Tx- will be pulled up by default and be transmitting data
4. The 3rd of them, Rx, will be floating until we connect the other end of the
line

That information seems enough to start trying different combinations with your
UART-to-USB bridge, but randomly connecting pins you don't understand is how you
end up blowing shit up.

Let's keep digging.

A multimeter or a logic analyser would be enough to figure out which pin is
which, but if you want to understand what exactly is going on in each pin,
nothing beats a half decent oscilloscope:

![Channel1=Tx Channel2=Rx](https://i.imgur.com/HuEshXs.png)

After checking the pins out with an oscilloscope, this is what we can see in
each of them:

1. GND verified - solid 0V in the pin we suspected to be GND
2. Tx verified - You can clearly see the device is sending information
3. One of the pins floats at near-0V. This must be the device's Rx, which is
floating because we haven't connected the other side yet.

So now we know which pin is which, but if we want to talk to the serial port
we need to figure out its baudrate. We can find this with a simple
protocol dump from a logic analyser. If you don't have one, you'll have play
"guess the baudrate" with a list of the most common ones until you get readable
text through the serial port.

This is a dump from a logic analyser in which we've enabled protocol analysis
and tried a few different baudrates. When we hit the right one, we start seeing
readable text in the sniffed serial data (`\n\r\n\rU-Boot 1.1.3 (Aug...`)

![Logic Protocol Analyser](https://i.imgur.com/OkHJtsA.jpg)

Once we have both the pinout and baudrate, we're ready to start communicating
with the device:

![Documented UART Pinouts](https://i.imgur.com/znXRocn.jpg)

## Connecting to the Serial Ports

Now that we've got all the info we need on the hardware side, it's time to start
talking to the device. Connect any UART to USB bridge you have around and start
wandering around. This is the hardware setup to communicate with both serial
ports at the same time:

![All Connected](https://i.imgur.com/aU83qTd.jpg)

And when we open a serial terminal in our computer to communicate with the device,
the primary UART starts spitting out useful info. These are the commands I use
to connect to each port as well as the first lines they spit out when you turn
the router ON

![Boot Sequence](http://i.imgur.com/t43E8dm.jpg)

```
[...]
Please choose operation:
   3: Boot system code via Flash (default).
   4: Entr boot command line interface.
 0
```

'Command line interface'?? We've found our way into the system! Let's try it out.
We press `4` before the timeout to start in command line mode:

```
-------------------------------
-----Welcome to ATP Cli------
-------------------------------

Login:
```

Username and password requested? This could be an issue. Before panicking, let's
try a few common combinations. What about admin:admin??

```
-------------------------------
-----Welcome to ATP Cli------
-------------------------------

Login: admin
Password:    #Password is ‘admin'
ATP>shell

BusyBox vv1.9.1 (2013-08-29 11:15:00 CST) built-in shell (ash)
Enter 'help' for a list of built-in commands.

# ls
var   usr   tmp   sbin  proc  mnt   lib   init  etc   dev   bin
```

After finding the `shell` command in the ATP Cli help, we finally have access to
linux!

## Next Steps

Now that we have acces to the BusyBox CLI (linux-ish) we can start nosing around.
Depending on what device you're reversing there could be plain text passwords,
TLS certificates, useful algorithms, unsecured private APIs, etc. etc. etc.

In the next post we'll focus on the software side of things. I'll explain the
difference between different boot modes, how to dump memory, and other fun things
you can do now that you've got root.

Thanks for reading! :)
