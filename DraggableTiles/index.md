---
layout: page
title: DraggableTiles
---

In VB I could make a label object that I could drag around the screen, which functioned as sort of a 'draggable tile' -- the box and the text were draggable.  The label would accept non-wrapped multiline text, which is critical for me.

I'm looking for the same idea in Cocoa.  The few 'texty' IB objects that drag, like NSTableView (drag only whole columns or rows only, it seems, not cells) and NSTextField or NSTextView (drag the text itself and not the field) don't seem to do the trick.  Any suggestions?

----

As far as I know this is something you're going to have to implement yourself.

----
If I remember correctly, this is something you have to implement yourself in VB as well. This might get you pointed in the right direction: DraggingNSViews.

