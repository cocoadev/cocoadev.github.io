---
layout: page
title: ReadSerialStreamWithUsbtoSerialConverter
---

  

Hi all,

I'm working on a project and am hoping to use cocoa to do some data interpretation. Here's the deal:

I'm developing an actively calibrating surround sound system. I'm using ultrasonic tracking modules called crickets http://cricket.csail.mit.edu/ to track the user's location in a room. The cricket receiver module passes location data in a serial stream:

Transmission speed 115200 bits/second
Data format 8 bits, no parity
Flow control Xon/Xoff (�software�)
Stop bits 1

Since my G4 powerbook has no serial ports, I'm using a USB-serial converter. I wish to create a cocoa utility to interpret the serial stream, extract the location data from it and pass this in a usable form to my audio processing software.

I just don't know where to start, I've been searching the web to find out how to read in a serial stream to no avail.

Any adviced would be greatly appreciated.

Regards

Pete

mail@pete-jones.com

----

Do you know about AMSerial?

