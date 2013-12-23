---
layout: page
title: CocoaStrokepathEquivalent
---

Does anyone know if there is the equivalent of the PostScript operator, "strokepath", which replaces the current path with a path which describes the outline of the line itself? Maybe I haven't described it very well, but, if strokepath were to exist, then bezierpath strokepath] fill] would be the equivalent of [bezierpath fill].

The reason I want this is that I want to invalidate the region occupied by the keyboard focus ring and I figure that saying [[self superview] needsDisplayInRect: rect] won't cut it.

When the focus is changing, I can say [self setKeyboardFocusRingNeedsDisplayInRect: [self bounds and that sorts everything out. But I want to remove my NSView subclass entirely from its parent.

It seems that removeFromSuperview: doesn't invalidate the focus ring region in the superview (nor should it). But I feel saying [self setKeyboardFocusRingNeedsDisplayInRect: [self bounds]] from within removeFromSuperview: is a bit strange.

I figured if I could find the path of the outline of the focus ring itself, I could then say self superview] needsDisplayInRect: [[bezierpath strokepath] bounds. But that will only work if there is a strokepath operator and it seems there is not.

There must be an easier way!

denty.

