---
layout: page
title: LineNumbers
---

**How to display line numbers alongside an NSTextView.**

Every once in a while a question pops up on the Cocoa mailing lists about how to create a left margin with line numbers, such as in Xcode.



== Approach 1: Custom NSRulerView == 

You can subclass NSRulerView and draw the numbers there. An example of that is the NoodleLineNumberView in [https://github.com/MrNoodle/NoodleKit NoodleKit] along with the a more recently updated version: https://github.com/scottharwell/NoodleKit 



== Approach 2: Custom NSTextView ==

The trick is to subclass NSTextView with an NSTextContainer that allows for writing in a margin.

See http://home.earthlink.net/~kvddrift/software/linenumbers.tar.gz (link has been fixed)

*This method ony works when NSTextView's setWidthTracksTextView attribute is set to YES.  This is a severe limitation since this attribute will almost certainly be set to NO when implementing non-wrapping text; see DisablingWrapInTextView.*



== Approach 3: Custom NSScrollView Tiling ==

You can also do custom NSScrollView tiling, adding your own custom NSView subclass alongside the text view and doing whatever you want. No need to customize NSTextView or NSRulerView.



== Old == 

I made a complete line numbering library. Have a look.

http://homepage.mac.com/mnishikata/page4/page9/page9.html (BROKEN LINK)

http://homepage.mac.com/mnishikata/page4/page9/files/page9_2.png (BROKEN LINK)

