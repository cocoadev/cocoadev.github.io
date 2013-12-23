---
layout: page
title: TotalControlOverTextAndFonts
---

After some research, I'm a bit confused about what I should use to gain the most control on fonts, sizes, text, glyphs, etc... There's plain Quartz 2D, General/CoreGraphics, ATSUI... ATSUI is a bit complicated and it seems to be Carbon only, and I'd like to avoid carbon for the moment, unless it's essential for what I'm asking for. Yet, ATSUI seems to be quite low-level and fitting. Is there something equivalent to ATSUI in the Cocoa world ?

-- Trax

----

Quartz2D is General/CoreGraphics.  It has the ability to draw glyphs, but it can do no layout, so it's basically useless if you want to draw strings with accented characters or something more sophisticated, and you do not want to do your own layout (which you'd do based on the info retrieved from the font tables, but this is a lot of work, no-one does it!).

ATSUI does the unicode layout and renders text, true it was created before OS X, but you can use it from Cocoa -- the only real problem is that many of the coordinates are in FIXED, which is a 16 bit integer and a 16 bit decimal, limiting the range to +/- 32768, but you can add a transformation to the graphics context if you need larger coordinates -- the actual string data is in     General/UniCode, which is compatible with Cocoa's     unicode type, but you would need to convert back and forth from General/NSString, if that is your current storage format.

The Cocoa way is General/NSLayoutManager -- I think this is probably as complex as ATSUI, but in another way, it's object oriented, so it might be easier to affect the layout (though ATSUI has a lot of callbacks)...

Initially I choose ATSUI for General/TextMate, because it can synthesize bold and italic for fonts that hasn't got that as a drawn style, but through some work, this can now also be done with General/NSLayoutManager, and for on-screen rendering General/NSLayoutManager does a nicer job, since it rounds glyph sizes to integers, and thus need to use less anti-aliasing (although this style can be forced with ATSUI as well).

--General/AllanOdgaard

----

I am drawing text with ATSUI (yes, I know, not really Cocoa) -- the problem is that when the background color is not white and/or the text is not black, bold makes the (synthesized) font thinner (rather than thicker/stronger).

I have seen mention of this problem elsewhere, but I know that one guy "solved it", but he don't know how, so I wanted to learn if anyone here knows a fix?

I would think that perhaps telling ATSUI about which background color I use, would fix it, but I can't see any tags to do that.

----

I have an ATSUI-related question, I'm placing it here since there is no reason to clutter this Wiki with unanswered ATSUI questions.

Here goes: How do I disable ligatures when I am drawing text with ATSUI?
