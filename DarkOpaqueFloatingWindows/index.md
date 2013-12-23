---
layout: page
title: DarkOpaqueFloatingWindows
---



I've noticed a lot of dark opaque floating windows showing up in applications lately. For those of you who don't know which ones I'm talking about just launch iPhoto choose an image and click on the adjust button. I've searched and searched and can't seem to find any direction on building something like that. Is it a subclassed NSWindow, or is it something completely custom? I've seen it it in more applications than just Apple's (including Panic's "Coda") and so that suggests to me that it might be matter of subclassing an NSWindow and init-ing it with the proper parameters. If someone could point me in the right direction I would appreciate it.

----
Like these?

http://www.seanpatrickobrien.com/2006/09/28/ilifecontrols-10/

http://shiira.jp/hmblkappkit/en.html

--CristianDraghici

----
Yes exactly! Thank you, CristianDraghici. You are the most amazing.

----

Otherwise known as HUD windows (for "Heads Up Display").

----
Thanks for all your replies. Sean Patrick O'Brian's iLifeControls was exactly what I was looking for. I couldn't find any example code for implementing the HUD on his sight, and being a bit new to Cocoa and OS X's dev tools I wasn't sure how to get started. I googled and came across this:

http://dev.lipidity.com/apple/ilife-controls-hud-windows-and-more

There is a sample project on this page that helped me a lot. Turns out the controls are not that difficult. Once you get the framework stuff handled ...and I haven't ...just drag the header files for the controls and window into IB's classes section. Throw down a panel and add the controls you want and set their custom class in the inspector to O'Brians classes.

I'm finding the Mac Dev community very friendly. Thanks a million.

----
Hey there, I just wrote some code to produce windows like this, it works quite nicely, I'd be happy to send it to anyone who would like it to look at and use.
Nice tip on the example stuff for O'Brians, I too found it a bit impenetrable.

