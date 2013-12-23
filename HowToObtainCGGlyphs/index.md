---
layout: page
title: HowToObtainCGGlyphs
---

How do I access the glyph table of fonts? i.e. if I have a string which I want to draw using CGContextShowGlyphs then how would I go about transforming the string to glyphs?

I do know that this transformation is not simple, i.e. several characters can result in a single glyph and a single character can result in several glyphs.

I also know about NSLayoutManager and that ATSUI is the more low-level system for text layout and rendering. But from where does ATSUI get the information?

I have looked at FM (FontManager), ATS (Apple Type System) and more, but have not been able to figure out how I do manual layout.

*okay, I found a private method in NSFont which can do it:     _defaultGlyphForChar: -- now does anybody know if Quartz can draw text in bold? I tried to adjust the line width, but that does not seem to be respected by the font drawing function.*

*For the records; I do know that some fonts have a special heavier version, but this is far from the rule, and ATSU can synthesize bold when a particular font is missing.*

*Currently I simply draw the text over the old one with different offsets, but it does not look that good.*

----
*okay, I found a private method in NSFont*

Ouch, see NSLayoutManager      - (unsigned)getGlyphs:(NSGlyph *)glyphArray range:(NSRange)glyphRange;  and friends.

See Also: http://developer.apple.com/documentation/Cocoa/Conceptual/TextLayout/Tasks/DrawingStrings.html

*I am fully aware of these things, but it is very slow because it needs to do layout to obtain the glyphs (and it's rather cumbersome to setup). Also, I do not have my text in a text storage, and it does not give detailed information, i.e. it transforms a string to a glyph array, but as there is no 1:1 mapping, it does not give me the information I am after.*
----

Ok, Fair enough. It is difficult to decern what you are **really** trying to accomplish other than the fact you feel you need to use Core Graphics to perform your task.

*now does anybody know if Quartz can draw text in bold*

There are a number of ways to draw text in both Cocoa and Core Graphics. In Cocoa      NSAttributedString - (void)drawAtPoint:(NSPoint)point;  and      NSAttributedString - (void)drawInRect:(NSRect)rect;  jumps to mind.

----

Yes, as hinted above I do know most about Cocoa text rendering (and ATSU as well). The problems with the NSString/NSAttributedstring approach are many, it has a bug which affects clipping when an affine transformation is used (which can be the case for me), it cannot synthesize italic (well, with 10.3 I can use NSObliquenessAttributeName), it is again very slow, it does only allow for a full string to be drawn (and not parts of it), the text measurement support is really bad, and as above, I do not have my text as (attributed) strings nor my styles as a conforming NSDictionary style object -- also, my text has tabs, and when drawing (attributed) strings I need to supply an array of tab stops, which I really have no idea about how long should be.

Trust me, I know what I want, why I want it, how I could workaround wanting it, and why I would not prefer to do so! :)
----
OK, I trust you. ;)

On the     _defaultGlyphForChar issue. As a thought, you could put a breakpoint on     _defaultGlyphForChar and gain some insight that may be obvious from the disassembly. A solution derived in this manner would be better than using a private method, in my book anyways.

I am going to take a little closer look at Core Graphics text rendering techniques, if I come across anything that may be of interest, I will stop back.
----
Thanks for the trust! :) and yes, debugging the private method is a good idea, to see if I can learn about any public API that it might use.

Regarding Core Graphics, the CGShowGlyphs says it uses ATS for the information, but I have not been able to find any interesting bits in ATS, there is also a picture showing this releationship:

http://developer.apple.com/documentation/Carbon/Conceptual/ATS_Concepts/art/fontservices.gif

Interestingly, there was an entry in the Safari weblog about fonts not acting exactly as "the other system", because they used a more lowlevel API (and there were some hardcoded values in Cocoa, like not to anti alias fonts smaller than 8 points etc.), and in the debug menu of Safari one can *enable* "Use ATSU" (which screws up the layout). So it would seem that Safari (like me ;) ) prefers to avoid both Cocoa and ATSU, so what is left? Does it also use Quartz directly and/or ATS?

----
In the TrueType and OpenType standards, there is a required font table ('cmap') with information for mapping from character codes (in one or more source text encodings supported by the font itself) to glyph codes.  All TrueType and OpenType fonts are required to have this character mapping table.  The standards also define other tables for font metric information which are used in layout.  ATS (including relevant parts of FM) provides several APIs for accessing font table data given different methods of identifying font objects, and can sometimes synthesize various font tables when required.

I suggest the original poster read through the TrueType specification, and then look at FM/ATS documention for reading font table information.  Obtaining a CGGlyph out of all that is the next step.
----
Ah, thanks a lot! I had looked at     ATSFontGetTable, but I thought the     iTag was to be a predefined ATS constant, but it would seem to be defined in the OpenType standard then -- I will have a read there!

Do you know if this will work with all font types, i.e. postscripts fonts and whatever ATS may support (altough OpenType seems to be a wrapper for ps ad ttf)?

*Okay, now I have played around with the cmap table, but I seem to be missing something -- e.g. the Monaco font has entries for various classic Mac encodings and one for Microsoft (as platform) with PRC as encoding (which I have not been able to find any specifications for, but it seems to be a People's Republic of China-encoding). But there is no Unicode entry, and the combined set of the classic Mac encodings do not seem to suffice.*

*Ahh... -- it seems that the font also contain the postscript names of each glyph and these can be mapped to unicode...*

**Okay, I finally managed to make it work -- for most fonts the cmap data is useless because they only cover a subset of the glyphs in the font (Microsofts recommends placing a unicode mapping table in the cmap, but most of Apple's fonts do not have one), so instead one must iterate the postscript names of the glyphs (where the first 258 have a name defined by Apple, i.e. not in the font), and map these to unicode -- of cause not all fonts have names for their glyphs... so all in all, it is a lot of work ;)**

And now I am back after some days... CGContextShowGlyphs is not really meant for drawing strings of text, as it does not support e.g. combining glyphs... so back to ATSUI (expressing all my coordinates as Fixed, copying all my text into UniChar buffers and so on ;) ) -- well, I didn't really expect it to work, but it had to be tried anyway -- one of the free ADC videos showed a slide with all sorts of international (i.e. hebrew, arabic etc.) text rotated, scaled and similar drawn on screen, and the speaker said that "this was drawn with Quartz" (I think it was one of the videos relating to Quartz), but surely he must have meant to say ATSUI, or perhaps he ment drawn by Quartz, but one glyph at a time, placed by ATSUI...

----

Safari uses CGContextShowGlyphs (or a variant); you can see it doing so in the WebKit source code. As you rightly say, though, the Quartz functions are very low level and only render glyphs; they don't do any work combining glyphs or organising right-to-left rendering, or any of the more advanced features that you get for free with Cocoa and ATSUI. If you look at Safari's code carefully, you'll see that it currently uses ATSUI for awkward cases but drops back to Quartz for the simpler ones. -- AlastairHoughton

----

The Trick on the iPhone is to use the undocumented CG function 
<code>
extern bool CGFontGetGlyphsForUnichars(CGFontRef, unichar[], CGGlyph[], size_t);
</code>

