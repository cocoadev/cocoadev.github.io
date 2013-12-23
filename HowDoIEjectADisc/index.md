---
layout: page
title: HowDoIEjectADisc
---

I have been trying to create a very basic Cocoa application using Xcode and Interface Builder in OSX Panther... Yada, yada, yada. But basically, my program is a basic and easy DVD Player. Unfortunately, it's becoming very hard to try and get DVD's to play. DOES ANYONE OUT THERE HAVE ANY BIT OF CODE FOR IT?

I heard that you may just be able to play DVD's in Carbon, but I know NOTHING about Carbon...

So, I decided to work with the little part of my application, the controller. I put a small 'Eject' button on the interface and created a class to get it to work. Unfortunately, I can't find any code to get it to Eject... Is there any way to make that button eject the CD or DVD?

-=-=-=-=-=-=-=-=-GOT CODE?-=-=-=-=-=-=-=-=-=-

At least help me out... Please. I like to hear people's ideas. I'm a beginner.

----

It's just my opinion, of course, but perhaps if you are just a beginner, you should be tackling a project that is a little easier. I've been programmming in Cocoa off and on for several years, and I don't know how you could start on this. There are Darwin/Unix level programs that allow you to do things with the disk drives, but it does not appear as if you are prepared to embark on that yet. Just because your interface only has a few controls, that does not necessarily mean that you are creating a "very basic Cocoa application". At sourceforge, there may be a DVD ripper open source project or two for Unix-like OSes, so you can see what you're in for. To research your question, you could start by reading the man pages for diskutil or hdiutil. If you don't understand what those man pages are telling you, then you are probably not yet equipped to undertake a project of this nature. There is a page I found for you at this site: QuestionForIPod that mentions the NSWorkspace method unmountAndEjectDeviceAtPath. So far, this page and that one are the only pages at this site that do so. I'd say you should google for pages that refer to that method. Dig in.

I understand your dread. I googled for "Cocoa DVD player" and all I could find was rental condos in Cocoa Beach, Florida equipped with DVD players.   :-D

----

Take a look at this thread on CocoaBuilder: http://www.cocoabuilder.com/archive/message/cocoa/2004/10/4/118835

----

Apple actually came out with an example not too long ago that does exactly what you're looking for: a DVD playback app. http://developer.apple.com/samplecode/CocoaDVDPlayer/CocoaDVDPlayer.html

