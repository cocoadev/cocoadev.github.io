---
layout: page
title: AppleSpecificCodePoints
---

    
 static const struct { char const* const name; unichar const code; } KeyGlyphs[] =
 {
    { "pb_enter",     0x2324 },
    
    { "left",         0x2190 },
    { "up",           0x2191 },
    { "right",        0x2192 },
    { "down",         0x2193 },
    
    { "ib_left",      0x21E0 },
    { "ib_up",        0x21E1 },
    { "ib_right",     0x21E2 },
    { "ib_down",      0x21E3 },
    
    { "home",         0x2196 },
    { "end",          0x2198 },
    { "return",       0x21A9 },
    { "pageup",       0x21DE },
    { "pagedown",     0x21DF },
    { "tab",          0x21E5 },
    { "backtab",      0x21E4 },
    { "shift",        0x21E7 },
    { "control",      0x2303 },
    { "enter",        0x2305 },
    { "command",      0x2318 },
    { "modifier",     0x2325 },
    { "backspace",    0x232B },
    { "delete",       0x2326 },
    { "escape",       0x238B },
    { "numlock",      0x2327 },
    { "help",         0x225F }
 };


The reason for the pb_enter is that Project Builder depicts the enter key with another glyph than the rest of the system, and the same for the ib_left/right/up/down.

Please add code points as you see fit!

--AllanOdgaard

----

http://developer.apple.com/documentation/Carbon/Reference/Menu_Manager/menu_mgr_ref/constant_14.html

The F-keys don't appear to be associated with a Unicode code point, but they are in Lucida Grande's glyph repertoire shown in Font Book. The technote at http://developer.apple.com/technotes/tn2002/tn2079.html shows how to support unencoded glyphs.

----

Which for Cocoa programmers would be something like:
    
 NSGlyphInfo* glyphInfo = [NSGlyphInfo glyphInfoWithGlyphName:
    @"F1" forFont:lucidaFont baseString:@"F1"];
 
 NSDictionary* attr = [NSDictionary dictionaryWithObjectsAndKeys:
    glyphInfo,        NSGlyphInfoAttributeName,
    lucidaFont,       NSFontAttributeName,
    nil];
 
 [attributedStr addAttributes:attr range:rangeOfF1Substring];


--AllanOdgaard

