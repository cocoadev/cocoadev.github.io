---
layout: page
title: NSTextStorageHowTo
---

I'm trying to do a simple task, but being overwhelmed by the size of the NSText class, I can't find what I'm looking for.

I want to put a specific piece of text in a NSTextView. That text comes from a NSMutableAttributedString. I will eventually need to do the opposite : put the NSTextView's content into a NSMutableAttributedString. Anyone know how to do that simply ?

-- Trax

----

Sure. 
    
 [textView setString:string];

--CharlieMiller


----

Also, the text storage for the NSTextView ( retrieved with <code>[textView textStorage]</code>) is a subclass of NSMutableAttributedString, so you can just read that instead of creating a new string when you want to examine the NSTextView's contents. Or you can copy it with <code>[[NSMutableAttributedString alloc] initWithAttributedString:[textView textStorage]]</code>. -- Bo
----
Well, [textView setString:string]; works, but only with NSString objects. I want to do such a thing with an NSAttributedString.

-- Trax

----

The idea is that you already have an NSMutableAttributedString that's holding the text view's contents in the form of its text storage; since you can do anything with an NSTextStorage that you could with an NSMutableString, there shouldn't be much of a need to copy in or out.  However, for when you must, the text storage's -setAttributedString: method lets you copy in an external NSAttributedString and its -mutableCopy method copies out.  -- Bo

