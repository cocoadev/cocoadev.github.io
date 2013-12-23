---
layout: page
title: DarkHighlightColors
---

Actually, I have two questions. 

The first, which is embarrassingly straightforward, is that I'd like to get the dark version of the highlight color set in the system preferences. To see what I mean, notice that the highlight color for selected text is the color you set in the system prefs, whereas in tables and outline views you get a darker, more saturated version for highlighting. I want to get the latter color -- because in my current situation, I need high-contrast ( and I'd like to respect user color preferences ). For what it's worth, I've tried several of the NSColor class methods for highlight colors, but they all seem to return the light color -- not the dark. I imagine I've just overlooked some method... (but there are so many).

My second question is, where on this wikki (and how) can I post some sample code? I wrote a really light and useful category on NSBezierPath this morning to make rounded rectangles really easily... and I bet somebody, someday, may find it useful. I went to CocoaSampleCode ... but I'm not certain how to add something to it.

--ShamylZakariya

----

Answer to question 2: you add a new page to the wiki using WordsJammedTogether.  So to add a new sample code to the CocoaSampleCode page, click Edit, then make up a wiki word for your topic.  When you save the page the wiki word will have a question mark next to it.  Click the question mark to create a new page. Might I suggest RoundedRectangles ?

--SaileshAgrawal

----

Sailesh's suggestion is very good, and your sample code sounds excellent--it's the sort of thing I'll be wanting in a day or two.

----

I believe     [NSColor alternateSelectedControlColor] is the color you're looking for.  -- Bo

Hey... it worked. I'll be damned. I thought I had tried them all. Also, I've submitted a RoundedRectangles category in case anybody wants it. --ShamylZakariya

