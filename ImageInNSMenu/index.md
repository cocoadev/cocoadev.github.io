---
layout: page
title: ImageInNSMenu
---

Hi,

It's a simple question, but I can't find any help anywhere for it - I have an NSMenu, very basic, I want to attribute Images to some of the individual menu items. How can I achieve this? 

The reason I want to do this, is that I have a set of icons that show the status of some things in my project, the menu allows you to change the status those things, and it would be a much more complete use of the icons if I could have the relevant icon associated with the text in the menu. (was that understandable?!?)

Thanks for any help or advice!

-Peter

----

In NSMenuItem:

setImage:(NSImage)menuImage

----

Is there any way to set the menu item's image in Interface Builder?  I don't see anything among the Attributes or Connections panels for a menu item.

