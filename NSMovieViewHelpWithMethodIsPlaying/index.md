---
layout: page
title: NSMovieViewHelpWithMethodIsPlaying
---


I'm trying to do the following, but it doesn't work.... The program seems to hang as though isPlaying is always YES even when the movie gets to the end. How can I have my Cocoa/ObjC program wait until a movie is done before proceeding? (The movies are approximately 1 second long each.)

    
[myMovieView setLoopMode:NSQTMovieNormalPlayback];
theMovie = [aMovieItem theMovie];
while [myMovieView isPlaying];		// This becomes an infinite loop, apparently, even if I put in an NSLog command as the body of the loop.

[myMovieView gotoBeginning:self];
[self aKeyPressed:@"Q"];			// This is what should happen next, once the movie is done playing.


Thanks!

-dan

----

How about using a timer to check     isPlaying

----
Well, my concern is that isPlaying seems to stay true when the movie gets to the end. It's as though a "finished" movie still retains its "isPlaying=YES" status. Has anyone else had this problem? I know a similar issue was mentioned elsewhere in this site, but the solution was simply to attach the movieView to a window. This one is already part of a window. Have I missed something else?

----
Your code doesn't let the runloop run, which may be interfering with NSMovieView's processing. That's why he suggested using a timer instead of a busy-wait loop.

----
Yeah, that's pretty lousy code I wrote, I suppose. I've used a timer in a slightly different way (just call the selector I need after 1 second, which will allow the movie to complete regardless of when the timer is started), and that seems to have done what I need. Thanks for the advice!
-dan

