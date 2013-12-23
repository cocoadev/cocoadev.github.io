---
layout: page
title: CocoaSound
---

see also:

NSSound

CocoaSoundWithQuickTime

QuickTimeQuickStart

CoreAudio (not a Cocoa API)

Does anyone know how to monitor sound from a Cocoa app? What I mean is monitor the sound that is currently beeing played by another application? Is this even possible with the current Cocoa sound classes?

Also besides NSSound what api's are there for me to do sound processing?

-- ObjectiveMolz

----

As far as I know, CoreAudio is a straight C API and there are no Cocoa wrappers. I haven't had time to learn much about it yet, so hopefully someone else can fill in the gaps.

Also, Carbon sound APIs are available to your Cocoa app, I believe.

-- StevenFrank

----

Well I got no problems with straight C API's, but I still don't see any convienent way to get the sound currently beeing output.  It looks like I will investigate trying to record off the output device and see if that works. 

Of cource this is truely my first program to deal in depth with sound (i.e. more than just playing one) so im not even sure you can do this on a mac sound card...

-- ObjectiveMolz

----

I messed around with this awhile back and was able to get some things working.  It's been awhile since I touched it, but basically I took a couple of routines from the CoreAudio example code that comes with the developer tools  /developer/examples/coreaudio.  I don't remember which example I used (daisy or HALTest), but one of those has a routine for implementing play through, and that's what I was using.

Anyway, what I did was to take the current sound buffers, process them, and mix them back in to the current sound playing on the machine (adding delay).  I also had a simple wave display showing the current audio output graphically.  It seemed to work at the time.

Joe Schiwall
MacTelligence.com

Some things have changed since the 10.0.0 release.  My above discussion was based on work I had done on the Public Beta, and this code does not work on 10 final.  A note from Apple on the CoreAudio mailing list confirms this:  currently, you can not access the systems output buffers.

