---
layout: page
title: DrawingOverQuickTimeMovie
---

Hello,

I am trying to draw over a QuickTime movie. I have my own NSMovieView, place a subview over it and draw in the subview. But it seems that the QuickTime movie is drawn at last over all other views. Is it possible to do it in another way, for example using drawRect method or something similar?

--ThomasSempf

*By 'my own NSMovieView' do you mean an instance of NSMovieView or an instance of a subclass you've made? You might want to try the latter.*

----

I have my own subclass and also tried to rewrite the drawRect method. I first called [super drawRect:aRect] and then did my own drawings. But it doesn't help, cause the movie is always drawn over all other things.

--ThomasSempf

----

OK, it seems that it's not possible to change the compositing behavior of a QuickTime movie in a way to put a NSView over it. I followed now the example from Apple and put a transparent NSWindow over it. Take a look at: http://developer.apple.com/samplecode/Movie_Overlay/Movie_Overlay.html

--ThomasSempf

