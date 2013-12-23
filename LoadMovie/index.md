---
layout: page
title: LoadMovie
---



Hi, can someone tell me how to load a movie into  a NSView from the computer (open)?

----

Head over to: HowToOpenAMovieAndPutItInAMovieView

----

When i load i movie with different size than the movie view it scales to fit. How do i set it to fit proportionally?

----

I don't think you can with the standard NSMovieView - you'd need to subclass and override     -(NSRect)movieRect

Karelia (the Watson people) have a class to do this - [http://cocoa.karelia.com/AppKit_Classes/NSMovieView_subclas.m]

