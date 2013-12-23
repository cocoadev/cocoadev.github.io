---
layout: page
title: HowDoIGrabDVDVideoFrame
---

I've been experimenting with Apple's CocoaDVDPlayer example:
http://developer.apple.com/samplecode/CocoaDVDPlayer/index.html

My experiment is an attempt to try to get the current frame image from the dvd window and then send it to an image view, just to see if it works. But it doesn't.
I've placed the following code in the VideoWindow class, which is called by a timer several times a second.
    
NSLog(@"timer firing");
self contentView] lockFocus];
currentFrame = [[[[NSImage alloc] initWithData:self contentView] dataWithPDFInsideRect:[[self contentView] bounds];
self contentView] unlockFocus];


Of course, it's not getting any pixel data when I ask for that image.

It looks like the DVD framework draws to the video card directly somehow. Is there any efficient means by which we can get that data into something that can be converted to an [[NSImage?

... Alternatively, is there any way that I can composite that video into a QCRenderer or some other technique for use with QuartzComposer compositions (rendering layers of images above and below the DVD video)?

----

Well, it'd be a violation of the Digital Millenium Copyright Act, which applies to anybody in the United States, so most of CocoaDev can't help you. See, DVD Player is written so that you can't grab frames. If you could, you'd be able to make a perfect copy of the DVD, which would be essentially rendering the DVD's copy protection useless (efficacy of the CSS algorithm aside.) As such, Apple has to make it difficult for you to make these copies so that they avoid being charged under the DMCA.

----

Being as I'm European, and under copyright fair use, I'd like to say you can look into using source code such as that used by handbrake (http://handbrake.m0k.org/). You'll need to decrypt (perhaps illegally) the VOB files, demultiplex the MPEG-2 stream to retrieve the video stream and then use either Apple's MPEG-2 quicktime component or maybe a third-party one such as libavcodec (I think) to extract the video frames. If you choose to do this, it will be a steep learning curve but all the code to do it is pretty much "out there" so it's an integration job rather than having to write too much code from scratch.

