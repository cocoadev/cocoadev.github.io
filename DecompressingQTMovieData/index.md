---
layout: page
title: DecompressingQTMovieData
---

I've created a QTMovie instance from a file, and verified that the file plays correctly in Quicktime Player and other apps, but there seem to be issues between certain video files and my app. All of the problem video files have one thing in common: when I do a "Get Info" on them, they have a codec of "MPEG1 Muxed". I also have a few rare instances where users try to use MPEG2 or something of that sort, and the audio plays back while the video is replaced with a white screen (when I render it out to screen using Quartz Composer). The simplest solution I can think of is to use writeToFile:withAttributes:, but I don't know how to strip out the MPEG compression or to "de-mux" it with that method. Does the QTMovieFlatten attribute do that?

