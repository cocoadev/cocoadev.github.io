---
layout: page
title: HowDoIPutImagesInMenuItems
---

Newbie question maybe and I am expecting an RTFM answer but:

How do I get an image to the right of a menu item?

----

A quick search on Google using "NSMenuItem site:apple.com" turned up this as the second link:

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSMenuItem.html

Searching that page for 'image' quickly turned up

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Protocols/NSMenuItem.html#//apple_ref/doc/uid/20000249/BAJGCIGF

I've never used setImage. Dunno if it's as simple as calling it... but there you have it. And yes - Google is your friend. Use the Cocoa documentation, and if that doesn't work, use google to search. I've solved more problems that way than most any other.

--TimHart

----

He wants the image to the right of the menu, in which case he needs to go to that link, then scroll up a bit and check out     - (void)setAttributedTitle:(NSAttributedString *)string

----

Doh! Didn't really pay attention to where they wanted it - or where setImage would put it. Thanks.

--TimHart

