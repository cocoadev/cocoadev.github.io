---
layout: page
title: DockIconBadgePlacementQuestion
---

Hello Cocoa Gurus!

I have a question for the Dock Icon badge. This is pure cocoa app btw

I have managed to get it over the icon but it's to the left instead of the right and I cannot seem to be able to move it..

Is it my brain that is off :)??

/J

----

Keep in mind that the point 0,0 starts in the *lower left* of the view. When composing any image, to get it to be further right, you'll have to increase the x component. Using drawAtPoint and the like, you can specify the coordinates easily. I've done the same thing you're doing effortlessly this way. A simple, friendly RTFM to you. ;-)

----

Thanks, I knew it was a supid one :). Brain now rebooted ;)

