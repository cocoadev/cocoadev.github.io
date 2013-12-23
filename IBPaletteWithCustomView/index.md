---
layout: page
title: IBPaletteWithCustomView
---



I'm going crazy... For the last two days I've been googling for a solution to my problem with a custom IB palette and change of font - here we go:

I've created a custom IB palette consisting of a single custom view(NSView). The view draws a bezierpath and some text within and the view gets its color settings, width settings, etc. from the provided inspector with relevant controls for controlling those attributes.

The ONLY thing left is change of font for my view(the text string) - I can simply not get a simple message or get changeFont: called for my view when IB's font panel changes font/size.
I've tried virtually everything as advised on the net: implemented acceptsFirstResponder, changeFont, even self window] makeFirstResponder:self] - the latter doesn't even force a call to acceptsFirstResponder! :(

So my question is: how can a custom view in a custom IB palette get notified/respond to font changes made through IB's font panel?

Please, please, please help me out on this issue - it's the only thing left for 100% success with custom palettes in IB.


/Mads

P.S: Please notice that the text drawn in the custom view is NOT done through a [[NSTextField or similar....

----

How are you drawing the fonts in the view? Are you using NSString's **-drawAtPoint:withAttributes: **? If so, I know that one can change the typeface by using the NSFontAttributeName standard attribute constant. Looking at the list of standard attributes,

http://developer.apple.com/documentation/Cocoa/Conceptual/AttributedStrings/Articles/standardAttributes.html

 you can change the font colour with the NSForegroundColorAttributeName constant. Here is how to use those constants,

    
    NSFont  *font   = [NSFont fontWithName:@"Garamond" size:11];
    NSColor *color =  [NSColor greenColor];

    NSMutableDictionary *attributes = [[NSMutableDictionary alloc] init];
    [attributes setObject:font  forKey:NSFontAttributeName];
    [attributes setObject:color forKey:NSForegroundColorAttributeName];

    NSString *msg = @"I hope this is green and in my favourite font; Garamond.";
    NSPoint point = NSMakePoint(0,0);
    [msg drawAtPoint:point withAttributes:attributes];


I guess you just need to make the NSColor some outlet of your controller class, so the change will get into your view? I've never bothered making an IBPallette, I've hear it was a bit tricky.

Cheers.

-DJF

