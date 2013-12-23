---
layout: page
title: CrossFadingAudio
---

In my cocoa app, I need to have have two mp3 tracks playing one after another, but I'd like to be able to add a fade between them. What is the easiest method of accomplishing this?

I've taken a look through the CoreAudio, and Audio Unit docs, but if anyone could point me in the right direction, i'd be very appreciative.

*Can't you just start the second track playing at 0 volume (before the first track finishes), then bring the volume up gradually on one and down on the other? I don't know much about Cocoa Audio frameworks, but this shouldn't be too hard. --JediKnil*

JediKnil is right, use an NSTimer to adjust playback volume. It will not be perfect, because of NSTimers resolution (consider making a seperate runloop), but all the coreaudio solutions are rather more complex. If you really want true crossfading, you will have to invest time into learning coreaudio and probably audiounits. These are challenging API's which require a lot of effort to learn, and it will take a while. You can hook up a MatrixMixer to do fast, good quality mixing. Alternatively you can investigate MusicKit, it has an easy and almost complete interface, but may be too big a framework to load just to mix two files. If what you are doing is for a general purpose mixer (ie, for a game, Instant Messenger, FTP client, etc...), then the NSTimer solution is good enough. 
-Jeremy Jurksztowicz

You could also use QuickTime.

