---
layout: page
title: SettingInsertionPoint
---

As a first project, I'm building a custom HTML editor (for myself, for now).  I'd like it to be able to insert empty HTML elements with a single keystroke.  I'm pretty sure I know how to connect menu items with functions that will do this via key equivalents.  My problem is that I can't figure out how to move the cursor programmatically so that it will be placed between the HTML start and end tags once the element has been added.  I've read the docs for NSTextView and superclasses, but haven't found anything that tells me how to move the insertion point.

Any ideas?

--  TIA, BorgCopyeditor

----

OK, I did manage to figure out what to do.  There's no method for just setting the insertion point, but you can setSelectedRange for an NSTextView and just hand it an NSRange with the location where you want the insertion point and length = 0.

Here's my (crude, but for now effective) method for inserting a paragraph element.

    
- (void)insertP:sender;
{
    NSRange currSelection;
    int newLocation;
    [theTextView insertText:@"<p></p>"];
    currSelection = [theTextView selectedRange];
    newLocation = currSelection.location - 4;
    [theTextView setSelectedRange:NSMakeRange(newLocation, 0)];    
}


For my next trick, I will try to generalize this method so that it can figure out which element to insert on the basis of the sender, instead of having one method for each kind of HTML element.

----

Well, that'll work, but a better way is to work on the text view's text storage object with mutable string methods. Like (untested code - you could probably just pass @"<p></p>" below as well)

    

theTextView textStorage] insertAttributedString:
       [[[[NSAttributedString alloc] initWithString:@"<p></p>"
                                       attributes: [theTextView typingAttributes]] 
                                          atIndex:[theTextView selectedRange].location]



insertText: is part of the user text input system and will pass through any input managers or whatever the user has installed, which you probably don't want. It also won't work if the text view is set non editable.

----

Thanks for the suggestions.  I'm using plain vanilla text, so attributes aren't necessary for what I'm doing now, but I guess if I eventually wanted to add syntax coloring, this could come in handy.  In any case, NSMutableString contains an insertString:atIndex: method, which I suppose would also avoid any conflict with input managers.  I began by manipulating the NSTextView object directly since I was thinking of my problem mainly in terms of the cursor position after the insert.

This text architecture stuff is fairly complicated, but I'm impressed at how accessible the simple stuff is.

-- BCE

----

It *is* complicated, but I guess that's the price you pay for being able to make 14-line word processors and the like

