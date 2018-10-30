---
layout: post
title: Emulating a USB Mouse with mbed to Cheat at CookieClicker
tags:
- embedded
- mbed
- c++
- cookieclicker
- starcraft
---

Some time ago, I was testing mbed's USBMouse and USBKeyboard, and used
[CookieClicker](http://orteil.dashnet.org/cookieclicker/) for the proof of
concept.

The idea of these libraries is that the microcontroller will tell the computer
"Hey, I'm a mouse" or "I'm a keyboard", and we will program it to send the key
presses or movements we want. This can be used for all kinds of reasons, such as
security attacks: "Hey, I'm a keyboard. Launch the terminal, execute these
commands, close the terminal.", but it can also be used for fun. Here, the mbed
will identify itself as a mouse, and send tons of left clicks without any delay
between them.

Thanks to the mbed's libraries, you don't need to configure the low level stuff
for USB communication, and the code is as simple as this:

{% highlight c++ %}
#include "mbed.h"
#include "USBMouse.h" //The library to work as a mouse

USBMouse mouse; //Declare the object mouse
DigitalIn myInput(p5); //Set an input to control when to send clicks

int main() {
    myInput.mode(PullDown); //Internal pull-down in the input pin
    while (1) { //Forever:
        if(myInput.read()==1) //If the input button/switch is enabled
            mouse.click(MOUSE_LEFT); //click
    }
}
{% endhighlight %}

And here's a video of the device in action:

<iframe width="560" height="315" src="https://www.youtube.com/embed/IKbpxfed_fg" frameborder="0" allowfullscreen></iframe>

Note: The computer does not actually lag like in the video. That was because of
the screen-recording software.

For the sake of keeping this post short, I'm not going to post schematics, but
it's a really simple circuit: The switch is connected between the pin p5 and
Vout, and the USB was an old USB cable cut in half, soldered to some header
pins, and connected to the mbed's USB pins as explained
[here](https://developer.mbed.org/handbook/USBDevice).

In my quest for fun, I also wrote a program with the USBKeyboard library to send
the key presses 0 to 9 time and time again. I replaced the switch with a button,
started a StarCraft 2 game, created 10 random control groups, and kept the button
pressed a few times during the beginning of the game. This way, we can inflate
our APM (Actions Per Minute) automatically. Here is the result:

![StarCraft sky high APM](https://i.imgur.com/EMGDRSY.jpg)

I'll have to try it again, pressing the button during much more time (That 736
APM is just the average APM for all the game), just to see how high it can go.
Could it be possible to overflow a variable somewhere? We'll see...
