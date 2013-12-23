---
layout: page
title: MPThreePlaybackAgain
---

Okay, I have an app that ab-so-lu-te-ly need to playback MP3 files correctly. I don't know if you encountered this kind of problem in the past, but I did.

I load the MP3 file, by URL reference in a NSMovie instance, and everything works... Except that, during runtime, the playback stops after 15-20 seconds. But not the progress cursor. And eventually, it restarts about 30 seconds later. I use the command so the task is maintained by QT :
    
MoviesTask(qtMovie,0);


With no satisfying result...

I'm desperate. There must be a way to do it correctly. Some time ago, I also tried mpg123, but I could not decipher everything, and when I followed directions, I got an error in the makefile process...

-- Trax
----
This might be your problem: http://www.scifihifi.com/weblog/mac/NSMovieViewAndMP3s.html  -- Bo
----

Actually, it did not help. I ran the sample code "as is" and it did not work properly. The MP3 obtinately stops after 10-20-30 seconds...

-- Trax

----

Very easy fix. Have a timer call the view to refresh every 5 seconds. Everything play's nicely then.

----

Yes, asking the movie to refresh is the right thing to do. Apple does this in many of their QT examples. Making the QT call "MoviesTask" does this without having to ask a view to redisplay.

    
    Movie qtMovie = [(NSMovie *)cocoaMovie QTMovie];
    MoviesTask(qtMovie, 0);

