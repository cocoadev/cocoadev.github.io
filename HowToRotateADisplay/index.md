---
layout: page
title: HowToRotateADisplay
---

Describe HowToRotateADisplay here.


Hey guys,

I am currently working on a small app that will live in the tool bar and provide quick easy access to rotating the screen from landscape mode to portrait mode.  Right now i can accomplish this by using some pretty sloppy AppleScripting but know there must be a better way to do it.

I've researched a lot of sites but no one seems to have hit on this one yet.  The only functions I can find on the Apple Developer site relate to Quartz Display Services but I'm not really sure if this is what I'm looking for.

http://developer.apple.com/documentation/GraphicsImaging/Reference/Quartz_Services_Ref/index.html

Does anyone out there have any ideas on any functions or frameworks I should be using to accomplish this?

thanks!
jarod

----

The only place I could find any reference to screen rotation is in IOKit/IOGraphicsTypes.h. There appear to be structures for getting and setting screen rotations, though I have no idea how or where they are used. But it might put you on the right track.... --GrahamCox

