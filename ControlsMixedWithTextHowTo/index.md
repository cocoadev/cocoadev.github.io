---
layout: page
title: ControlsMixedWithTextHowTo
---



I want to have a text view (within a scroll view) that has, after certain words in the text, a button or some other control. For example, in the text:

    
There are 2 blue cars at the intersection.


I might want to programmatically add a pop up button and a color well in the positions shown here:
    
There are 2 [NSPopUpButton] blue [NSColorWell] cars at the intersection.


The controls would function as a way of changing the text. That is, using the color well to select a different color from a color list (where colors are named) would result in the word "blue" changing to the new color's name. Other than that, the text would not be editable (or even selectable). I'll know which words need controls after them, but I won't know much else about the text (e.g., line length, number of lines) until it's read in except that it will be a "plain text" file.

I'm really not sure how to approach doing this in Cocoa. Do you know of any examples that are like this that I can look at? Or just some general ideas about how best to implement this?

----

    
NSPopUpButton *aPopUpButton = [[NSPopUpButton alloc] 
     initWithFrame:NSMakeRect( 10, 10, 98, 26 )
     pullsDown:NO];
	
[aPopUpButton addItemWithTitle:@"item1"];
[aPopUpButton addItemWithTitle:@"item2"];
[aPopUpButton addItemWithTitle:@"item3"];

// assume textView is a IBOutlet to your NSTextField
[textView addSubview:aPopUpButton];


That should get you started.  The controls will scroll normally and everything. Position in a text view are relative to the upper left corner. The addSubview: method is defined in NSView, NSTextView is a subclass of NSView.

Maybe someone else can tell you how to place the controls relative to certain words.

----

If you want your controls to act like they're part of the text (automatically moving when you add text, etc.) then you should investigate NSTextAttachment. You will probably need to create a custom NSTextAttachmentCell, but it will be able to mostly pass things through to an internal NSCell, so it probably won't be a huge amount of work.

----
Has anyone actually done this? How would one put a button cell's contents in an attachment cell? It's my understanding you can't have 'cells-within-cells'.

----

NSTextAttachmentCell is the name of both a protocol and a class which implements that protocol. Looks like you'd have (at least) two options if you want to implement the idea above. You could subclass NSButtonCell and implement the NSTextAttachmentCell protocol, or you could create a subclass of the NSTextAttachmentCell class that works as a proxy for a NSButtonCell.

----

Thanks! It's the latter I'm interested in, since I want this to be a reusable solution for several cells that aren't mine. What general steps do I need to take? General will do, since I can look the specifics up in the docs (and ask about what I don't understand).

----

The general idea is that you create an instance of NSButtonCell (or whatever) and set an instance variable in your NSTextAttachmentCell subclass to point to it. Then you pass all relevant messages along to that instance, and simply return the results. Your NSTextAttachmentCell subclass therefore looks and acts like an NSButtonCell (or whatever). Cocoa provides a slick way to do this sort of thing without having to implement (or even know about) every method that the proxied class implements; check out the documentation for NSProxy, and also WhenToUseProxies here. I don't know if an NSProxy subclass that implements the NSTextAttachmentCell protocol is really what you want here, it might just be easier to roll your own. But reading up on those things will at least get you thinking in the right direction. And no, I haven't tried this myself, so let us know if it works out for you.

