---
layout: page
title: LimitedPrecisionOfFloats
---

I am creating an NSView subclass embedded in a scrollview, it's a hex view for a hex editor, the problem is, with large files, this view gets so high, that the precision of floats starts to interfere significantly. I.e. the space between lines is uneven and the cursor/selection is not always drawn in the same place as the text (e.g. if the text is scrolled into its place and the cursor is then afterwards drawn in terms of absolute coordinates).

I am afraid I need to take over everything myself, i.e. scrolling, coordinate transformations etc., which also mean I need to create my own NSScrollView/NSClipView replacement (since my view will never be larger than the visible rectangle).

But before I attempt at this, I wanted to hear if others are facing similar problems and/or have other suggestions?

*If at all possible, try making the window wider and limiting its height by that means. It seems to me that this bit with floats could be a problem with CoreGraphics, and... well, I can't see that working out terribly well.*

----

Yeah, I wrote a subview for the same purpose and ran into the same problem. I ended up using two controls for the view. One control was a scrollview just like yours, and the other control allowed you to jump across large sections of the document in megabyte steps. I think having separate controls is better for viewing large documents anyways since the scoller can only give you so much precision. 

----

I don't suppose anyone would consider making their hex editor view(s) available?  I've done a bit of searching around and haven't managed to find one.  I'm loath to write yet another one given that there must already be so many floating around.  I don't even need it to support more than 32K of data.

----

I had a different (but similar :>] ) problem with a Wave View class that showed potentially millions of samples. I wrote my own NSScrollView *subclass* (along with an NSClipView subclass) which handled scrolling and zooming. It is not as hard as it sounds. -JeremyJurksztowicz

