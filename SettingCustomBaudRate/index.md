---
layout: page
title: SettingCustomBaudRate
---

I am not sure if this is a good place to ask this question or not but here goes...

I am trying to write a small Cocoa app to communicate with a serial device. I am using a KeySpan USB to Serial adapter. The main problem I have is that the device I am connecting to uses a non-standard speed (8192bps) and I get errors when I try to set the speed. Is there any way to set the speed to 8192?

I am using some libraries I found for the communication - AMSerialPort by Andreas Mayer. 

I am pretty new to Cocoa and MacOSX development so please bear with me.

Thanks

DaleB
----
Well, interestingly enough I'm also writing an app which uses a keyspan USB -> serial adapter. I'm using libserial, which is a teensy tiny c api for serial port io. I got libserial from Freshmeat, like 2 years ago back when I was developing under linux... and a search just now returned no results so I gather the project's dead. If you want it I've got the source -- it works fine on OS X.

However, it's a C api, and pretty basic. I wrapped it in C++ to make a nice threaded/asynchronous buffering system.

Anyway, you might find it useful to drop to C for this kind of work.

--ShamylZakariya
----
I like the Keyspan units, they work well.

To the OP. I don't use AMSerialPort, I have been opening the the file handle manually (using open, and then setting the comm attributes with the BSD termios APIs) and then managing (Cocoa friendly) asynchronous I/O by passing it off to an NSFileHandle. I did a quick test with my configuration (Keyspan Adapter/Driver) and it does accept 8192 as a valid baud rate (set with *cfsetspeed* in my software). So, it is either your port/adapter, driver, or AMSerialPort that doesn't like the speed. The BSD level APIs don't seem to have a problem with it.

- NeilVanNote

