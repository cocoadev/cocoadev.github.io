---
layout: page
title: ClassicEnvironment
---

Does anyone have any freaking idea how Apple managed to get classic Apps to display properly under OS X?

It seems that the two types of drawing (QuickDraw vs. Quartz) would lead to some pretty messy situations.

----

Probably they implement QuickDraw in terms of Quartz. Remember that e.g. Carbon apps don't need to use Quartz, they can still use their QuickDraw calls; remember also that Cocoa has NSQuickDrawView or whatever it is.

----

If you mean Carbon windows, I think Apple pretty much gave it the appropriate level of abstraction and the problem solved itself.

CoreGraphics (aka Quartz) does support vector graphics, but it's equally at home with bitmaps. Most windows are double-buffered, but they certainly don't have to be. A Carbon window is more-or-less just a special bitmap window. Quickdraw draws bitmaps, so it just takes the bitmap and draws into it normally via an ordinary CGrafPort.

QD lets you do things like reference a graphics port's bits directly; the LockPortBits and UnlockPortBits APIs were introduced to help Carbon apps do this. Apps can call them, and that gives the system a clue that drawing has occurred so that it can do the right thing and flush the changes appropriately. They actually got their own section in the Carbon Porting Guide. 

http://developer.apple.com/documentation/Carbon/Conceptual/carbon_porting_guide/cpg_prepstruct/chapter_2_section_4.html#BABBHJHG

Classic (the BlueBox / OS9 compatibility layer, as opposed to Carbon, which is a native API) is a bit messier because memory was not protected and apps could write directly to the screen. But it's still basically the same idea -- remember, Apple writes OS9 and OSX both, so they could hack the emulated OS9 toolbox to interact with the OSX environment where appropriate. Although Classic's a harder problem than Carbon, it's still not intractable.

Everything inside Classic is literally running inside a kind of virtual machine - Classic.app - so there are a lot of sneaky ways to make things work. For example, the "screen" that a classic app sees doesn't have to actually be the real screen. **[Obligatory Matrix (NSMatrix?) reference: "Do you think that's _air_ you're breathing?]** And so on. I'm sure it took a lot of pizza, but the OS engineers made it happen...

-- DrewThaler

