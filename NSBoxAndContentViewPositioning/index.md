---
layout: page
title: NSBoxAndContentViewPositioning
---

I've got a short box (height=26) with the title positioned NSBelowTop. The goal is to display the contentView to the right of the title. If this was the box:  [ theTitle  (theContent) ]

I've tried overriding resizeSubviewsWithOldSize, resizeWithOldSuperviewSize and autoresizesSubviews. None of this seemed to help but maybe I'm missing the obvious.

I'll happily RTFM if someone can point me to TRFM (TheRightForkingManual). ;-)
-nrs-

----

I don't think boxes support this.  You could just have no title on the box and put a static text field right next to the box.  -- Bo

----

I haven't got it working properly yet but it seems like I'm getting closer...

I remove the title with [myBox setTitle:NSNoTitle]; And I draw my custom titleCell as/where needed.

It seems like I can't change the frame(or bounds) of the contentView to keep it from overlapping my titleCell. My content is an NSTextView so I changed the textAlignment to NSRightTextAlignment but that's not a real viable solution in the long run.

I've tried using an NSView as the contentView and placing my textView on that but that seemed to cause more problems than it solved. I may revisit that idea after I do some ReFactoring.

----

Not a good solution but it looks like I can override _tile: and short-circuit the box's contentView positioning. Woohoo!

