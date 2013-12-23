---
layout: page
title: WhatIsGlyph
---


----
After reading so many things about glyphs still i am able to distinguish between glyph, and charecter.
So..
What are glyphs?
How it is different from charecters?
----
In many languages, there is not always a direct correspondence between letters of the alphabet and their printed representation.  For example, in Arabic, the appearance of a particular letter depends on the preceding and following characters.  Arabic uses a "script" style of writing.  In other languages,  rules for using multiple letter/symbol forms add context to writing in a similar way to using capitals or lower case in English.

The bottom line is that there are often multiple different ways to graphically depict the same alphabetic "character".  Similarly, sometimes multiple "characters" condense down to one graphically depicted symbol or ligature.  Because there is not always a one to one mapping between alphabetic "characters" and their printed depiction, it is necessary to have some object other than "character" that represents the printed depictions.  In the Cocoa text system, Unicode strings and General/NSCharacterSet are used to represent alphabetic "characters".  General/NSGlyph represents a printed depiction.  General/NSFont and General/NSLayoutManager provide language specific mappings from alphabetic "characters" to glyphs.

Apple's documentation explains this all very well: http://developer.apple.com/documentation/Cocoa/Conceptual/General/FontHandling/index.html

----
For an excellent example of this, open General/TextEdit, switch to the Zapfino font, type out the exact text "Zapfino", and notice how the entire thing changes when you get to the "o". The font has a gigantic ligature for the word "Zapfino", which is a single glyph representing seven characters in sequence.
----
Thank you for your quick and wonderful answers. wih this knowledge again i wil be looking into the documet you have mentioned. will come back and eat your head if at all i get some more doubts!
