---
layout: page
title: NSViewRedrawsOnObjCMethod
---

In an NSView subclass (actually a ScreenSaverView subclass), calling some ObjC methods on the containing NSWindow inside drawRect: causes drawRect: to be immediately re-called. However, it doesn't happen when calling the equivalent function. For example,     self window] setOpaque:NO]; causes a redraw, while     _NSSetWindowOpacity([[self window] windowNumber], NO); does not. Same thing for     [[self window] setAlphaValue:0];  and     _NSSetWindowAlpha([[self window] windowNumber], 0);([[[NSView needsDisplay] returns YES during the first drawRect: call and NO during the second.)

I assume the _NS.. functions are not to be depended on in future OS releases...is there a more elegant solution than calling them?

----

One option is simply to have a little toggle in your subclass you set when you don't want a redraw to occur - have your     drawRect: code do nothing when this toggle is set. More flexible than calling private member functions.

