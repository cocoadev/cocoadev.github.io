---
layout: page
title: GettingFramerateOfAStreamingQTMovie
---



getting the rate of a QTMovie which has been inited with an RTSP stream is proving difficult.  The Quicktime Player has no problem providing this info.  how do they do it?  the standard QTMovie calls like 'rate' aren't working for me.  Nor does attributeForKey:@"QTMovieRateAttribute" return anything other than 0.00000

EcumeDesJours

----

since you're not able to get that info, did you manage to play/display that streaming movie in your application?
my experience is that it works fine as long as you use a QTMovieplayer object. but when i try to grab the frames to memory first, for instance if i want to manipulate them before display, i never managed to get any frame from a streaming movie.

arri

----

Or for an entirely different type of answer - did you search the quicktime-api list archives at www.lists.apple.com? I'm sure yours is a common question ...

----

I haven't found the answer yet to this issue.  My original goal was to create a tool for an art-installation to receive video streams and of course be able to reinitiate the connection if the stream stalled.  Since I was never able to determine whether the stream had stalled or not (eg by watching its frame rate drop to 0) I had to go with a different solution.  Quicktime player gives you the framerate of a live stream so it is obviously part of at least the older Pascal-based QT API but I couldn't find any obvious way to do it without getting way in over my head with the older API...

EcumeDesJours

----

One way to do this would be to use a display link and ask the movie if it has a new frame (google     CVDisplayLinkCreateWithCGDisplay()). Then set the user info for the callback set with     CVDisplayLinkOutputCallback() to point to the view playing the stream. There are thread issues you need to read up on, but there's a bunch of sample code at ADC that shows you how to use the     CVDisplayLink functions. 

The only catch with going this route is you have to draw the movie yourself in an OpenGL view using     QTVisualContextIsNewImageAvailable() and     SetMovieVisualContext(). --zootbobbalu

----(EDIT/squeezed in-between)
after re-reading you post i realise i don't undertstand something/or maybe miss an importantg detail here... you say 

".. set the user info for the callback ... to point to the view playing the stream .." 

i'm usually setting this userinfo to point to the class dealing with the rendering, which is not necessarily a view..and my problem is, i want to manipulate the frames BEFORE display.. i can't find much info on this -userinfo- pointer, but could this be a lead? can you elaborate on the importance/function of setting this user-info? - arri

----

You shouldn't have to draw into a OpenGL context. A CoreVideo context should work fine...

----
i was actually asking EcumeDesJours because i can't get core-video/displaylink to work with rtsp streams.
i'm using a     QTVisualContextRef (a core video image-buffer), but     QTVisualContextIsNewImageAvailable() just never reports new frames when trying to play an rtsp stream. 
it seems related to the absence of length/duretion/timebase info, because for an rtsp-stream embedded in a referencemovie that **does** contain this info, it works fine..

sadly, the qt-api list isn't of much help here.. try googling for     QTVisualContextIsNewImageAvailable and     rtsp...

arri

----

Well, I know next to nothing about QT, but afaik the rate as in QTMovieRateAttribute is the current playback rate (aka speed)
where 0.0 means stopped, 1.0 = 1x etc. I do not know how to get at the framerate though - I need to do this soon and I plan
to investigate the timescale stuff in QTTime.

BjoernKriews

----
as long as you don't have to deal with rtsp-streams, you're probably safe with documentation and examples available for quicktime in cocoa (QTKit framework ect) and/or related core-video api... (lucky you :( .. ) .arri

----

Right, folks.  The issue is that many normal methods such as attributeForKey:@"QTMovieRateAttribute" simply don't return any useful data when the movie in question is an rtsp stream, as opposed to a normal quicktime file, which does give you its framerate etc on request.  RTSP streams seem to be handled very differently... :-(  EcumeDesJours

----
as a dirty work-around you can embed the streaming RTSPmovie inside an empty quicktimemovie that contains a timebase/-line.
we managed to produce one in livestage-pro, and this works... i agree it's a very dirty (and expensive) workaround.. it's also very strange there's virtually NO docs or posts on mailinglists related to this subject. i'm currently trying to see if i can use code of opensource projects that i know can handle rtsp-streams. one i'm looking at is KeyWorx :) - arri

