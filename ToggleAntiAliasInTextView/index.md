---
layout: page
title: ToggleAntiAliasInTextView
---


Hi There,

I need to toggle Anti-Aliasing of text within a General/NSTextView control in Cocoa.
Im building a code editor and would love to lock the interface so you can only have monospaced fonts w/o aliasing, regardless of your global display settings...

Cheers

----

I've never tried this, so I may be totally off base, but here goes....

General/NSGraphicsContext has a     -setShouldAntialias: method. You could subclass General/NSTextView, override drawRect:, and set that to     NO before calling     super.
