---
layout: page
title: CharacterPalette
---

I'm interested in accessing the CharacterPalette programmatically. Ie, if someone activates an NSButton in some way, the CharacterPalette will show up on their screen. 

I'm assuming that the user has the CharacterPalette installed and that they are able to use the particular language my application is built for.

Any suggestions?
-- SabinDensmore

===

Use the usual methods of digging around. (Come back for the answer after you've figured it out - it will be a good learning exercise. Answer: make a button and set its target to first responder with action of orderFrontCharacterPalette:)

===

so, RTFM? I think that's what you're saying. Thing is, one needs to know the vocabulary in order to find the answer. Thanks for the vocab ;).

-- edit: looks like the above suggestion works only on OS 10.3 (according to Apple [http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSApplication.html#//apple_ref/occ/cl/NSApplication]). However, since I'm on Jaguar and can access the Character Palette, I see no reason why I shouldn't be able to allow my users to. I'll post the results tomorrow. --sd

----

The quick and dirty way.......

[NSApp orderFrontCharacterPalette:self];

ChrisW

----

Is there a way to do it from Carbon which would be available on 10.2?

