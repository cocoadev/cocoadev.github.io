---
layout: page
title: PalmBlueToothDevelopment
---

** Note: ** apparently someone else managed to figure it out: http://homepage.mac.com/jonassalling/Shareware/Clicker/


----

I've been trying to hack together something like Sony Ericsson Clicker (http://homepage.mac.com/jonassalling/Shareware/Clicker/index.html) except for the PalmOS, but I've been stymied...

Has anyone compiled the Palm Bluetooth sample code (from http://www.palmos.com/dev/tools/bluetooth/ ) using prc-tools? I haven't had any luck, and of course the sample project is for Codewarrior, on Windows.

-- MichaelMcCracken

(note, for info on prc-tools, see PalmOSDevelopment )

----

I can't speak about the Palm end of the connection, but I can say a few words about the Mac end. I spent a few days trying to get a Bluetooth proximity sensor working between my laptop and my Siemens S56 phone. I got it working, sorta, but after a period of time my phone's bluetooth stack crashed. So I gave up. 

My experience with the IOBluetooth API was very discouraging -- the API itself is bad, the examples are worse (won't even compile), and the documentation is somewhere in the middle (poorly organized and misspelled). You're going to need a fair amount of experimentation to get it working. And even then, I'm still not sure if my problems were caused by bad phone firmware, or by my IOBluetooth code. So good luck!

-- MikeTrent

