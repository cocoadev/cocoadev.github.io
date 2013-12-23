---
layout: page
title: LineNumberOfRangeInNSTextView
---

I need to get the line number of a range of text as displayed in a NSTextView. I've done some research and found NSString's 

    
� getLineStart:end:contentsEnd:forRange:
� lineRangeForRange:
� getParagraphStart:end:contentsEnd:forRange:
� paragraphRangeForRange:


methods, but I'm unclear how to get the line number of a specific range from it. All the examples I've seen have just counted lines. Does anyone have something they could paste in before I go and do the work myself?

----

Get yourself AppKiDo (or DocoaBrowser) and search for 'paragraph' and 'line' and look at all the results. Those methods are what you need to read about.

----

I have a range of a string inside a text view and I need to obtain the line number at which the string is located.  So far I have thought up one way that one may go about it: Determine the characters before the start of the match range and loop through them, counting the return characters.  This is, however, beyond my skill level- so could anyone point me in the right direction or tell me if sample code exists for this?  (Or perhaps my idea is completely wrong and you could tell me a better way to go about it).

----

There's no easy, direct way to do this because what constitutes a 'line' when wrapping is considered is entirely dependent on the width of the text view. However, you might want to search the docs for the     -paragraphs method ... that precludes the need to loop through looking for various kinds of line endings.

----
Wrapping is disabled the on the text view.

----
Just search for how many \n's before your range.location

