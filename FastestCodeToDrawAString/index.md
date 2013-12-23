---
layout: page
title: FastestCodeToDrawAString
---

more discussion at FastTextRenderingInNSView

I have to draw many strings and would like to know what the fastest way to do so is (NSString drawAtPoint:... is slow!).

The spec is:

- it does not have to text wrap, ever

- it needs to have font settings, but all the strings are in the same font


I've seen a lil about ATSUI/CGShowText but can't seem to find any sample code or any instructions on using it?

----

You can maintain your own NSLayoutManager/NSTextStorage/NSTextContainer trio, and use NSLayoutManager's     drawGlyphsForGlyphRange: to draw into your view.     drawAtPoint: is very inefficient, because it creates new instances of these objects each time it's called. 

See [http://developer.apple.com/documentation/Cocoa/Conceptual/TextLayout/Tasks/DrawingStrings.html]

Try that before messing with lower-level stuff.

----

Gave that a try, as well as ATSUI and CGContextShowText, and none of them provided a noticeable performance improvement ovar NSString drawAtPoint :-/ (ie. the overhead of the functionality I need (see above) makes them not any faster :-(

----

The slow thing with text on OS X is layout, so the advantage with NSLayoutManager/ATSUI over NSString is that the layout can be cached for repeated draws.

If you do not need this, you are unlikely to see any difference in speed.

Though CGContextShowGlyphs is fast, since it does no layout, but that means what you can draw is limited to either strings that need no layout other than normal glyph-advancement or what you can layout yourself. And you also will have to go through a lot of trouble figuring out how to best convert character codes to glyphs.

--AllanOdgaard

----

Text in OS X is fairly slow. It should be several orders of magnitude faster in Tiger thanks to Quartz 2D Extreme, but right now, path-drawing and text-drawing are absolute dogs. If you want really fast drawing, what you want is OpenGL. The system drawing code right now is optimized for prettiness rather than speed.

----

If you're not seeing any speedup, you might try approaching the problem from a different angle. How many strings are you drawing? Do you really need to draw them individually? Maybe you can 'coalesce' different strings into one and draw them in a batch with one drawing operation.

----

In the Examples provided with the Developer Tools, there is a demo called Worms.  It provides 4 different text drawing samples and shows you their relative speed in frames-per-second.  A lot of their speed-up comes from techniques already mentioned here, but it is a good concrete example.  --MikeSolomon

