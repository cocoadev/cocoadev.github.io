---
layout: page
title: NSMovieViewHelp
---



Here's my two problems.

1)  When I load up the NIB that NSMovieView is in, it looks all white until someone loads a movie.  Is there any way I can make it invisible until a movie is loaded?

2)  Whenever the window is resized, the movie stops playing.  Is there any way to allow it to continue playing?  I tried setting a -(void)windowDidResize, where it says [movieView start] but then it tries to tell me that movieview cannot take the method start... kind of odd.

Thanks for helping someone new to this... it means a lot.  Anyone who can shed some light is greatly appreciated.

-Gumpan

----

Not sure about 2, but for 1... add it programmatically, and not until you need it. That is, have it in your nib, but not in the window, and add it to the window at run-time.

-- RobRix

