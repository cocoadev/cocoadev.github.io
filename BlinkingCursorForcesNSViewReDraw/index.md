---
layout: page
title: BlinkingCursorForcesNSViewReDraw
---

I've got  an NSView subclass on my page, and alongside it are a couple of text boxes... but every time the cursor blinks in the text box it forces a redraw of the NSView subclass next to it. This shouldn't really happen. The two aren't overlapping. Is this normal behaviour? It seems a bit strange.

Thanks for any advice!

I've seen the behavior you're talking about.  What I ended up doing, which is far from perfect but was good enough for me, was to keep track in my subclass view of when *not* to redraw.  The draw method would get called and a conditional would immediately exit.  This requires some delegation to catch events that *should* cause the view to redraw, and you still obviously pay for the call overhead.  If you're working with simple text boxes the savings will be noticable and the remaining call cost should be negligible.

