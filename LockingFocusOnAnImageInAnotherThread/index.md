---
layout: page
title: LockingFocusOnAnImageInAnotherThread
---

I just figured out a problem with an app that was freezing because mutiple threads were waiting on the same lock. I have a GUI that draws a bunch of icons in a separate thread. This wasn't a problem when I wasn't drawing that many icons while the display was being refreshed, but every now and then the app would hang. After sampling the app when it was in a frozen state, I noticed the problem was multiple NSImages trying to draw at the same time. 

Apple's documentation says that NSImage is thread safe, so I was kind of caught off guard. 

Here's some of the links that helped me solve my problem.

http://cocoa.mamasam.com/COCOADEV/2004/02/1/83686.php
http://www.mail-archive.com/cocoa-dev@lists.apple.com/msg15412.html

Basically, to lockFocus on an NSImage in a separate thread safely, just ask the image to use a separate cache to do its drawing.     [theImage setCachedSeparately:YES];

--zootbobbalu


An update:  Your links no longer work.  You seem to be referring to http://www.cocoabuilder.com/archive/message/cocoa/2004/10/25/120154.

