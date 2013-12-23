---
layout: page
title: QTSoundFilePlayer
---

Kurt Revis's QTSoundFilePlayer is awesome :)  CoreAudio can be incredibly difficult to use, including the fact that there is no obvious way to feed it a sound file from disk. QTSoundFilePlayer utilizes QuickTime to convert any sound file into the data stream CoreAudio uses and allows nearly fully control over the sound playback.  I'm using it in Adium ( http://www.adiumx.com ) to allow us to manage sound volume and, via a small change to QTSoundFilePlayer, output device.  NSSound offers neither of these possibilities; QuickTime allows sound control but has tons of overhead and takes a lot of CPU while playing.

QTSoundFilePlayer is available in his PlayBufferedSoundFile sample program ( http://www.snoize.com/ ).

--EvanSchoenberg

----

**I've been looking for months about HOW TO CONNECT QTSoundFilePlayer to a effect AudioUnit.**
It seems that it's been developed under AudioUnits 1... and now 2 is being used... ???

I need a caritative soul to help me in this... felipe at baytex period net

--Felipe Baytelman

