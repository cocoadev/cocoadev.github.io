---
layout: page
title: TransparentViewWithNonTransparentSubview
---

I have a partially-transparent custom view, which has several subviews (General/NSButtons). The subviews show up with the same transparency as their superview -- is there any way to make the subviews opaque? Thanks.

----
General/PostYourCode. This should General/JustWork, as seen when you add stuff to a boring old General/CustomView in IB, which draws nothing by itself and is therefore completely transparent.

----
Make sure you've wrapped your drawing code in     - General/[NSGraphicsContext saveGraphicsState] and     - General/[NSGraphicsContext restoreGraphicsState] calls before drawing the subviews.

----
Thanks guys, but I found the problem... it was just a stupid mistake. In my subviews, I was drawing the image background using General/NSCompositeSourceAtop, when I should have been using General/NSCompositeSourceOver.

----

*Make sure you've wrapped your drawing code in     - General/[NSGraphicsContext saveGraphicsState] and     - General/[NSGraphicsContext restoreGraphicsState] calls before drawing the subviews.*

No offence, but that's just voodoo. Apart from the fact that graphics state is already being saved and restored by Cocoa around view drawRect: calls, how could this have helped? Saving and restoring the graphics state does exactly what it says on the tin - so if your code doesn't change the graphics state, or if it does but is only drawing its own stuff in that altered state, this "advice" is totally bogus. I don't know if there's a serious cost involved in the gstate save/restore, but in general it's not needed, so shouldn't be done "just in case". As I said, it's voodoo if you don't understand when and why to use it. Reminds me of locking Handles in the old days - people rarely understood that properly either. --GC.
