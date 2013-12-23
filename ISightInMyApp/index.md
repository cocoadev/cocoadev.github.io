---
layout: page
title: ISightInMyApp
---

Is there a nice API to capturing iSight input?  Also, does anybody know if it's possible to have two iSights and get stereo vision?
----
I believe it behaves just like other firewire cameras, and so you can access it through the Quicktime APIs, just like other cameras.
----
Brilliant idea! I can just imagine using a modified version of iChat with stereo glasses!

----

I'm having a hard time finding this in the docs, so can anyone tell me how to de-saturate video captured from an iSight? I just want the standard video coming in to be made black and white. Could anyone point me in the right direction please?

It's best to assume that my code is the same as the SGDataProc example from Apple (https://developer.apple.com/samplecode/Cocoa_-_SGDataProc/Cocoa_-_SGDataProc.html)

Take a look here: http://developer.apple.com/samplecode/WhackedTV/WhackedTV.html

----
Just so you know, don't even bother trying to get stereo vision with IOXpert's industrial driver. I have been trying for the past 3 weeks unsuccessfully. Two iSights is quite possible. You might end up getting a little less framerate just because of the bandwidth required to do two audio/video streams. I think I did the math once and you would get about full speed. Anyway, I just got my iSight this morning so I'll let you know how stereo works :). --ZacWhite

