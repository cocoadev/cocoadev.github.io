---
layout: page
title: ConectingToMobilePhone
---

Describe ConectingToMobilePhone here.

I want to make an application that basicaly uses a mobile phone as a remote control.
Lets say I have a nokia, plugged in via usb, I need to generate an event that my application could act on when someone phones the mobile number.
Or using bluetooth? Though my readings into bluetooth have not found anything that will allow me to generate a "yourPhoneIsCalling" event. Unless I'm looking in the wrong places?
Anyone have any pointers?


I have just realized this could have terrorist uses! I am not a terrorist. But if i were, i would be writing the program on a PC , as there seems to be a ton of 3rd party phone librarie's to make comunication with mobiles easier, but none for mac. I dont even know where to start?

----
This is very slightly paranoid. Almost *everything* could have terrorist uses.
----
I think you need to look into serial port communication. That you can communicate with modem using serial port. There are list of commands availabe to make call, receive call..i think u can even find out when your modem gets incoming call..       -mnas
----
Its a mobile phone that I want my application to recieve  a "caller event" from (rather than through modem and landline), I have searched but cannot find an 3rd party cocoa libraries that simplify comunication with such devices. I was wondering if anyone else had come accross anything, or where i could license such technology.
----
Even mobile phone has modem. There are commands to send and receive SMS. [same way you have commands for make and receive calls] I donot know in detail...you better google for 'AT Commands' or GSM Modem commands

----
If you want to have a bit of fun, pair your phone with your Mac using Bluetooth, then run     screen /dev/cu.yourPhoneNameHere in Terminal, and start typing modem commands and such.

