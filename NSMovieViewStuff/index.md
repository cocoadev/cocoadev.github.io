---
layout: page
title: NSMovieViewStuff
---



I'm making what is essentially a very stripped down MP3 player application.  I've recently decided to model the UI roughly after the iPod (in functionality, not appearance).

It's relevant to note that I'm doing this in Java on Cocoa, as my use of Java has made me realize that someone writing to the API should never know where things are in memory ;)

Anyways, the current issue I have right now, is that I can't seem to advance to the NeXT song (pun accidentally typed) once the current one is done playing.  NSMovieView.isPlaying() remains true even after it's finished.

Any suggestions?

     ~BlaKe

----

I'm going to say right now that I truly dislike NSMovieView. It looks like it should make my life easier, but it doesn't. I've tried to use NSMovieView to create an MP3 player as well as to play interactive QuickTime movies, and in both cases NSMovieView just failed to work right. I had the best results calling down into the C QuickTime API directly from within my ObjectiveCee code. For example, I found I had better results calling QuickTime's IsMovieDone method than calling NSMovieView's isPlaying method. I suspect NSMovieView is meant for drawing movies in a window and it just doesn't cope well with other kinds of things.

I thought there was a nice Java interface to QuickTime. If you just want to play MP3 movies, I strongly suggest calling into QuickTime directly and avoid NSMovieView. You can spend days staring at NSMovieView in the debugger and still not figure out why it isn't working ...

-- MikeTrent

----

Well, I've managed to make it do what I wanted.  I found something in comp.sys.mac.programmer.help from a couple of months ago.  Someone was having the exact same problem.  They found that if they added the view to an open window, NSMovieView.isPlaying() worked properly.  So I did just that (it's size is 0x0 pixels) and now it works properly.

I agree though, I'm not happy with Apple's implementation of NSMovieView.  But for now, this problem is solved, and it is really quite simple code now that it's done.

Perhaps this is a real bug in NSMovieView (wether it was meant to be used like this or not)? Anyone have a suggestion as to where to submit it to Apple?

 ~BlaKe

P.S. Love OldGlory.

