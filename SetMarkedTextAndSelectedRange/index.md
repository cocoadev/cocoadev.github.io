---
layout: page
title: SetMarkedTextAndSelectedRange
---

How should the selectedRange argument of the NSTextInput selector     - (void)setMarkedText:(id)aString selectedRange:(NSRange)selRange be interpreted?

As far as I understand marked text is not the same as selected text, so I would think that Cocoa wants me to insert 'aString' as marked, and then perhaps use 'selectedRange' as the insertion point?

I can only test this with accented characters, which are only one character wide, and here 'selectedRange' is at position one with a length of zero, so that would place the insertion point just after the newly inserted marked text (assuming selectedRange is relative to where ever the insertion point was before sending this message).

But is this interpretation correct? i.e. what about Japanese input, which values will I get for this?

And could it ever be that the length of the selected range is larger than zero?

Also, with the insertion point just after the marked text, there seems to be an implicit understanding that an insertText: invoked while there is marked text, will first remove the marked text, then move the insertion point backwards, and finally insert the string.

But then again, the input manager does not unmark the selected text, if the insertion point is moved while marked text is shown -- NSTextView will send an abandonMarkedText to the current input manager, if the insertion point is moved away from the marked text, but keep the actual text (which would otherwise be lost if a type-operation had happened)...

The NSTextInput API is really weird, and the documentation is close to missing... i.e. do I need to support markedRange, selectedRange and such, and if the selectedRange argument is relative to my insertion point for the insertMarkedText-selector, then should the range I return also be relative in some way?

----
I wrote an article about NSTextInput. Check it out:

http://yllan.org/wordpress/?p=231

