---
layout: page
title: SuddenMotionSensor
---

I was looking around the site and couldn't find any information about the Apple laptops' built in motion sensor, so I am writing what I have found.  As far as integrating this motion sensor into to Cocoa apps, there are two implementations I could find.

General/UniMotion http://unimotion.sourceforge.net

This is some form of library for interfacing with the motion sensor.  However, it is not Cocoa specific and does not support all laptops with the motion sensor.

General/SMSLib http://www.suitable.com/tools/smslib.html

This is a C framework for using the motion sensor.  It supports all current platforms (I'm assuming that it does not yet support the General/MacBook Air, however.)  It is the code written for the software General/SeisMac.  If you have experience with Cocoa, or even C, it should be very easy to implement.  If you are having trouble, sample code is included.
