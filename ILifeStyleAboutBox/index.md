---
layout: page
title: ILifeStyleAboutBox
---

This is probably a stupid question, but how do I create an about box like in the iLife and iWork apps? It looks pretty simple, but I have no idea how to make the scrolling credits.

Any suggestions?

Thanks.

----
Although I haven't looked at the iLife about boxes, if you want to do animated scrolling credits, you should probably have a look at NSViewAnimation. Create a view that displays your credits (or whatever), and set it inside another view that defines the boundaries of the scrolling area. Set up the NSViewAnimation object appropriately (read the docs -- it's easy) and start the animation running when you display the window. -CS

----

Thanks, I'll try that.

