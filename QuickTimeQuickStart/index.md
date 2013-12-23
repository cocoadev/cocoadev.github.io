---
layout: page
title: QuickTimeQuickStart
---

I'm making a Cocoa application that must be able to open a movie and convert it with QuickTime (actually, there is just sound in the movie). But the fact is: I don't know anything about QuickTime. And to convert my .mov I need more than just a NSMovie. Of course, I've tried to read Apple's doc about QT but it's a bit dark and not really sexy.

Do anyone know where to find a tutorial to get started with QuickTime programming? Or does someone have more information about movie conversion using Cocoa API (I think it's not possible)? Thanks.

Mathieu Godart

----

You might find some information in O'Reilly's MacDevCenter. I recall reading some very informative articles in the Java section about Apple's QuickTime for Java APIs... admittedly not Cocoa, but still, Apple's QTJ API's are apparently quite well done. I just searched, though, and couldn't find them. You might want to google, or look on developer.apple.com --ShamylZakariya

----

Check out PlayBufferedSoundFile at http://www.snoize.com/  It's a bit of code that opens a sound file as a QuickTime movie, uses QuickTime to convert it to uncompressed format and then plays it using CoreAudio.  -- Bo

----

If I want a quicktime player to resize proportionally, how would I go about doing that?  I can get it to resize, but never proportionally.

*I assume that when you say you want to resize the QuickTime Player proportionally, you mean that you want the QuickTime view (NSMovieView) to maintain it's aspect ratio when the enclosing window is resized.  I believe that this can be done by constraining the window size to the right aspect ratio and having the view grow with it (constrain the window, you constrain the view).  NSWindow has a setAspectRatio method.

If you want the window proportions to be free flowing, you may have to declare a delegate of the window and implement windowDidResize.  Then it could find the dimensions of the window and resize the view manually.*

Next up... How can I access how many seconds of a quicktime movie have played?  I know it has something to do with digging deeper into the API, but a little more help would really help to get me on my feet.

*I have never done this before but, obtain pointer to the QuickTime movie structure.  If you are using an NSMovie, QTMovie method will give you the pointer.  You should then be able to send the pointer into the following function *

    TimeValue tValue = GetMovieTime([yourQTMovieViewInstance QTMovie], nil);

*Example correct above with my own working code in a production app.*

*Send in NIL for the currentTime record.  The return value should be a TimeValue record where you can see how far into the movie the controller is.  Check the QuickTime API, Time and Space section, Working with Movie Time subsection.*

