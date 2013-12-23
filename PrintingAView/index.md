---
layout: page
title: PrintingAView
---

Basic introduction to drawing in Cocoa:

[http://developer.apple.com/documentation/Cocoa/Conceptual/DrawViews/index.html] 

----

Suppose I have a custom view in a window and I'm trying to print it using the print menu (actually, saving the file as a pdf from the print dialog).
What I'm getting, however, is the entire window being printed - not just my custom view.

Am I correct in assuming that what is happening is that the print command is calling the Window's -drawRect: method and not that of my custom view?

How do I just print my custom view with the print menu?

----

Does your view accept first responder status? The print menu item just sends a     print: or     printDocument: message to the FirstResponder (depending on whether your app is NSDocument-based or not). You want that first responder to be your view, not the window.

[http://developer.apple.com/documentation/Cocoa/Conceptual/Printing/]

----

I was desperately looking for an 'acceptsFirstResponder' method in NSView, and it was not there... I looked again after reading this, and it is in fact a method declared in NSResponder!! Not the first time I get caught by this issue: not looking in the superclass methods.

----

**Here's another example, originally presented under H**'elpWithSimplePrinting**

I was under the impression that a Document Based Cocoa Application came with the functionality needed to print.  I have seen a very simple example that does work.  However, when I create a new document based app, add an NSTextView to the MyDocument.nib window, compile and select the "print" menu item, nothing happens.

----

<debug 1>
Have you tested the NSTextView interface in IB?,  (Go to IB, File, Test Interface, scribble some words in your view, and then try to print them)
</debug 1>

<debug 2>
Is your text view the first responder when you are trying to print?,
</debug 2>

<debug 3>
Is your controller a subclass of NSObject?  (It should be, as if it's a sublass of NSTextView it may be "catching" some signals on it's own and not passing them on)
</debug 3>

----

Turns out that the difference was that the method  that the print menu item was connectd to was "printDocument:" and not "print:".
I admit that I'm not yet clear on the difference that makes, but it now behaves as I would expect.

Suppose I want to draw a rectangle of a particular color and beneath it, some text.  Can I do this in an NSTextView?

----

You could probably subclass NSTextView and use     - (void)drawViewBackgroundInRect:(NSRect)rect.

*Called when the text view intends to draw its background. Subclasses can override this method to perform additional drawing behind the text of an NSTextView.*

----

I guess what I really need is a good, simple Cocoa drawing tutorial.  Basic things, like "how to draw text at a specific location" or "how to draw a rectangle of a particular color."  That sort of thing.  The language reference, though complete, I find hard to dig out the basics in order to get started.

----

This is what I use.  

    

NSTextStorage *textStorage;
NSLayoutManager *layoutManager;
NSTextContainer *textContainer;

unsigned int unistrlen = 5;

unichar ch[] = { 0x0068, 0x0065 ,0x006c, 0x006c, 0x006f };

NSString *theString = [NSString stringWithCharacters:ch length:unistrlen]; 

textStorage = [[NSTextStorage alloc] initWithString:theString];
layoutManager = [[NSLayoutManager alloc] init];
textContainer = [[NSTextContainer alloc] initWithContainerSize:NSMakeSize(30*unistrlen,30)];

[layoutManager addTextContainer:textContainer];
[textContainer release];

[textStorage addLayoutManager:layoutManager];
[layoutManager release];

NSRange glyphRange = [layoutManager glyphRangeForTextContainer:textContainer];

[self lockFocus];
[layoutManager drawGlyphsForGlyphRange:glyphRange atPoint:NSMakePoint(10,10)];
[self unlockFocus];

[textStorage release];


