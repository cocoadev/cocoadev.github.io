---
layout: page
title: HidingObjectInNSCell
---

I try to let my NSCell hold an object, according to the documentation of objectValue: *To be valid, the receiver must have a formatter capable of converting the object to and from its textual representation.* -- this is rather problematic, because the textual representation is lossy, but without a formatter it will not use objectValue, instead it will use stringValue, even though my NSCell uses [self setObjectValue:obj] after each edit (but after completed edit, the data source will receive the textual representation).

So how to work around this limitation? perhaps provide attributed string with custom data in it or similar?

----

Learn about NSFormatter, and do what Cocoa says. :-)

*I just love these patronising replies from people who clearly have no clue of what they talk about, but sure try to sound like it*

----

As a clarification, how does editing work on your object if the text that's being edited is lossy?  If you don't want the object value to be changed even when the text is changed (which is what it sounds like to me) then you may want to use the -representedObject and  -setRepresentedObject: methods to store your object value.  -- Bo

----

Lossy might be an incorrect description, but this is in connection with CustomEditCells -- what I have is an NSCell which register itself with my application and thus get all key events, these are (in a processed version) stored as the object value, and I have some code which can convert it to a glyph representation, i.e. using the glyphs for shift, command, control, escape, delete, backspace etc. etc.

So I probably could also make code that took the glyph representation and recreated the key event, but if possible I'd really like to avoid this (since the two functions would then become dependant on each other, and there are glyphs with ambiguous meaning, like ^ for both the actual character and control (albeit these could often be resolved by looking at other characters)).

----

(Of course, ^ the text thingy and ^ the control widget can't be confused for each other, since control can never end a menu item (i.e., "command-control-s" not "s-command-control") and ^ must aways be at the end (same reason). The only confusing thing might be "control'^" but even that's not so bad.)

**
*I didn't say it was impossible -- just stupid to have an advanced parser to resolve ambiguities in a string that was created from an object, when we could just as well use the object -- and information like the numeric keypad qualifier would be lost when going from NSEvent -> NSString -> NSEvent*
**

OK, I see what you're trying to do here. You have a table of NSEvent<nowiki/>s, and you're trying to display and edit them in a table view. I think you're trying to leverage the cell metaphor to do something it wasn't quite designed to do - this whole conversation of taking binary data and "losslessly" converting into text and back seems to be at the core of the issue. Instead of returning NSEvent<nowiki/>s via the table manager API, instead store the NSEvent<nowiki/>s in an array (or something) behind the scenes and pass text information back up through the table view. Say column 1 is binding, and column 2 is command -- both of those could have been derived from NSEvent data prior to being displayed. The important thing here is that you don't directly "edit" the events via the table view -- instead editing is handled by the table view data source. 

I suppose I probably wasn't able to dissuade you from your current course. But I really don't think you're approaching this problem from the right direction...

-- MikeTrent

----

Well Mike, you can make me embrace whatever solution which will get the job done!

If you go to Project Builder / Settings / Key Bindnings then you'll see exactly what it is I am trying to recreate. But the heart of the problem isn't really this NSFormatter, I just thought that this would make some aspects of the implementation nicer (factor out the conversion to strings) -- but I have realised that there really has to be a 1:1 connection between the object and the string :-(

So I am back to fighting with CustomEditCells

