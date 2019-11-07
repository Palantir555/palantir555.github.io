---
layout: post
title: Quick development of bluetooth-based costume props using Arduino and ESP32
thumbnail: https://TODO <--------------------TODO TODO TODO TODO
tags:
- bluetooth
- costume
- halloween
- arduino
- lightning
- esp32
- wireless
- cosplay
---

We hosted a Halloween party for some friends last week, and I wanted to integrate
my costume (whatever it was) with the house decorations. I only had a handful of
evenings available to get everything up and running, so I had to build something
just complex enough to entice guests to play with it. Preferably using parts I already
had in the lab.

Here's the final product:

![Video: Final product](TODO)

This post describes the final solution, pitfalls I ran into, and the reasoning
behind some decissions. 

# The Hardware

The communications architecture is very simple:

![comms architecture diagram](TODO)

This is the circuit hidden in the cane/prop is called, in bluetooth GATT terms,
the `server`. It uses an accelerometer to detect sudden/abrupt movements. The
sensor is calibrated so the prop can be moved freely, and only hitting it,
tapping it on the ground, or other abrupt movements will trigger a notification
to the other device.

Since this part is gonna be smacked around, it's important to solder the connections
together, to avoid problems with loose wires.

![BT server circuit diagram](TODO)

It uses this hardware:

- 1x [`Olimex ESP32-EVB`](https://www.olimex.com/Products/IoT/ESP32/ESP32-EVB/open-source-hardware).
Any other ESP32 dev board should work; I just used this one in case I want to use
its on-board peripherals in the future
- 1x [`ADXL345`](https://www.analog.com/en/products/adxl345.html#product-evaluationkit)
accelerometer development module
- [optional] 1x button

On the other end, there's the GATT `client`. It is able to simulate lithining
using the LED strip, and play thunder sound files over the speakers.
It does so whenever it receives a notification from the server, and -if the
ambiance jumper is set- every couple minutes.

![BT client circuit diagram](TODO)

- 1x [`ESP32 Core Board V2`](https://docs.espressif.com/projects/esp-idf/en/latest/hw-reference/modules-and-boards-previous.html#esp-modules-and-boards-esp32-devkitc-v2).
Any other ESP32 dev board should work
- 1x `TIP31A` NPN power transistor. Used to drive the LED strip using
[PWM](https://en.wikipedia.org/wiki/Pulse-width_modulation)
- 1x `MP3-TF-16P` MP3 player module. It's a co-processor contolled over serial
to read audio files from an SD card and play them over headphones or speakers
- Analog LED strip
    - Some LED strips are controlled using digital signals, usually to make LED
clusters individually addressable. We want one of the simpler strips, controlled
directly trough the voltage you apply to it, so we can run it using PWM
    - I used an RGB, common-cathode strip (4 pins: +12V, R, G, B). But RGB are shorted,
      so we're controlling it like a white LED strip (2 pins: +12V, GND).
- Computer speakers
- Generics:
    - Perf board
    - Pin array headers
    - 2x 1Kohm resistors
    - Mini-jack connector
    - Wire spool
    - female-to-female wires (if not soldering everything)
    - 1x Jumper
    - Solder
    - [optional] female single line headers - to avoid soldering dev modules directly
- Off the shelf:
    - 12V power supply
    - USB power supply - Or a 12V to 5V regulator (I recommend [Traco Power](https://www.tracopower.com))

![Pic of both custom boards side by side](TODO)

# The Firmware

## Installing dependencies

TODO

## Client firmware

TODO

## Server firmware

TODO

# Decission Making

The concept is simple: Have a device hidden in a prop I can carry and pass around, 
and have another device connected to an LED strip and some speakers.
When something happens to the 

## Picking a wireless solution

There's plenty of wireless solutions available in the market that could be used
to solve this problem, from raw 433MHz radios to WiFi, LoRa, bluetooth, ZigBee...

In order to decide which one is best, first you need to lay down your requirements.
These were my requirements:

- Good for close and medium range: Approx. 15m radius, with walls and EM noise
disturbing the signal
- No need for an Internet connection
- Relatively low-power consumption on the transmitter
- Modern, consumer-grade protocol
- Privacy and security are irrelevant, since this device is not critical, not
dangerous, and is only gonna be used once (Watch out!)

Bluetooth 5 can certainly handle those requirements.

Bluetooth 5 can be used in different ways to optimize certain requirements: energy
consumption, data throughput, bidirectional communication, etc.
Using the Generic Attribute Profile (GATT), we can cover most requirements that might
come up in a costume. We can send a notification whenever an event happens in the
prop, and we can use attributes to report other status information (e.g. switches
in the prop could set the other device's behaviour).

If you're using this post to build your own project, and your requirements differ
from mine, make sure Bluetooth 5 is the right fit for you. And even if Bluetooth 5
is an appropriate choice, GATT might not be. Figure out what you need to optimize,
and find out what's the best fit.

Resources: [Bluetooth Core Specification](https://www.bluetooth.com/specifications/bluetooth-core-specification/)

## Picking the hardware

Picking the right microcontroller and development board for a serious project is
often a huge and rather complicated task. Fortunately, this project is just a
one-off, and my only concerns were delivery and development time. I was able to
use only parts I already had, except for the `MP3-TF-16P` module.

I'm very very familiar with the ESP32 from work, I have like a dozen of them at
hand, and I knew they work well with Arduino. It's one of the most popular
microcontrollers in the IoT market, specially for Proofs of Concept and DIY
projects. And it also supports WiFi, which could be useful in future versions.
Perfect fit for this project.

I picked the ADXL345 accelerometer because I had a few development modules at hand.

Picked the MP3-TF-16P because it was rather popular in the Arduino communities,
and was available for next-day delivery.

The LED strip had to be analog and not individually addressable so we could better
simmulate lightning using PWM. A white LED strip would work fine, but I only had
RGB ones at hand.

The power transistor had to be beefy enough to drive all 3 channels (RGB) for
the entire LED strip, and -less importantly- fast enough to handle PWM. I already
had some `TIP31A`s in the lab, and it can easily drive this strip's power without
breaking a sweat.

Everything else (power brick, perf board, resistors, pin headers...) is generic
stuff I had in the lab.

# Building on this

If you'd like to build your own project based on this one, here's a few suggestions
on where to start:

- Create a good costume for it (zeus, electric chair, boxer...)
- Fix connectivity issues. Under some circumstances, the client needs to be
restarted to re-connect to the server
- The custom board for the client should include a 12V to 5V regulator. Feeding
power from 2 different outlets is completely unnecessary
- Set up more clients in your costume/props/drink/home so more things react when
an event occurs
- Create 2 costumes that interact with one another 
- Find a good way to run the 12V LED strip off a portable battery, and integrate
another LED strip into the costume 
- Use more capabilities of Bluetooth GATT to create more diverse and complex
interactions

If this post is useful to you, don't forget to send me a video of your results :D

# Sources

Sources and inspiration:

- [How to use a ESP32 development board to read from an ADXL345 accelerometer](https://www.techcoil.com/blog/how-to-use-a-esp32-development-board-to-read-from-an-adxl345-accelerometer/).
- [Lightning and Thunder: Arduino halloween DIY project](https://oneguyoneblog.com/2017/11/01/lightning-thunder-arduino-halloween-diy/). I used this guy's thunder sound files, and lightning emulation loop

Technical documents:

- [TIP31A datasheet](TODO)
- [Olimex ESP32-EVB (Rev. F) schematic](TODO)
- [MP3-TF-16P datasheet](TODO)
- [ADXL345 datasheet](TODO)
