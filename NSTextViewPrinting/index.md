---
layout: page
title: NSTextViewPrinting
---

How do you print an NSTextView properly?  Any time I try it, it strips off a section on the right.  Can anybody help me? - RossDude
----
Printing is fairly simple in Cocoa. If you are doing basic print jobs, then there are two classes you need to understand in order to accomplish it:

An NSPrintOperation object handles the entire printing process and is normally created in the print method. In a **document-based application** you can override     printDocument: to handle the printing. In a **non-document based application** you may want to create a custom method to handle the printing and link the print menu action to it. When creating an NSPrintOperation, you specify the NSView you want to print and how you want it to print on the page using an NSPrintInfo object. If no print info object is specified, then it will use the shared print info object.

NSPrintInfo handles how the view is printed on a page (if it is centered, etc.). In a document based application each NSDocument has its own     printInfo object so each document can have its own print settings. In a non-document based app, you can use the     [NSPrintInfo sharedPrintInfo] class method to access a global print info object.

I print a few NSTextViews in a program, but I made a separate (non-drawn) window to arrange the printed parts. How big is your view? Is it near the right side of the page? Is it possible it's just that the margin settings are off? If you have a wide monitor (15" PB, for example), I think it's possible to create a layout that's too wide for the printer. Can you provide more info? -dan
----
I make a standard NSTextView.  In the program, I'll make an NSTextView that will have the margin settings so that the left side is at the zero mark and the right side is at the 8 1/2 mark.  When I go to print the thing, even if the NSTextView can contain all of the text, it chops off the section on the right, which seems to be somewhere around 6 1/2, or some weird number.  I have found this kind of thing with the ToolbarSample example as well.  If you try running that and printing from there, you get the same kind of thing I'm getting.  I hope that's enough info. :)

----

Here is how I do it in my document based text editor.

Make a copy of your existing NSTextView, set the properties on your printInfo instance, and you're done.

    
- (void)printShowingPrintPanel:(BOOL)flag
{

// set printing properties
MyPrintInfo = [self printInfo];
[MyPrintInfo setHorizontalPagination:NSFitPagination];
[MyPrintInfo setHorizontallyCentered:NO];
[MyPrintInfo setVerticallyCentered:NO];
[MyPrintInfo setLeftMargin:72.0];
[MyPrintInfo setRightMargin:72.0];
[MyPrintInfo setTopMargin:72.0];
[MyPrintInfo setBottomMargin:90.0];

// create new view just for printing
NSTextView *printView = [[NSTextView alloc]initWithFrame: 
	NSMakeRect(0.0, 0.0, 8.5 * 72, 11.0 * 72)];
//	[MyPrintInfo imageablePageBounds]];
NSPrintOperation *op;

// copy the textview into the printview
NSRange textViewRange = NSMakeRange(0, textView textStorage] length]);
[[NSRange printViewRange = NSMakeRange(0, printView textStorage] length]);

[printView replaceCharactersInRange: printViewRange 
	withRTF:[textView [[RTFFromRange: textViewRange]];

op = [NSPrintOperation printOperationWithView: printView printInfo: 
MyPrintInfo];
[op setShowPanels: flag];
[self runModalPrintOperation: op delegate: nil didRunSelector: NULL 
contextInfo: NULL];

[printView release];

}

-kinch

Thanks!  I'm not that great at doing printing stuff.  This is just what I need!  Thanks again. - RossDude

*Actually, thanks to Cocoa's modular text system architecture, you can create the entire text system beforehand and keep it around for later. Just add this kind of code in a method that's called once before you print (maybe even     awakeFromNib, and     printView will automatically keep track of any changes to your regular text view. (Note: this was taken from Apple's Text System Architecture docs) --JediKnil*
    
NSTextView *textView; // IBOutlet
NSTextStorage *textStorage = [textView textStorage];

NSLayoutManager *layoutManager = [[NSLayoutManager alloc] init];
[textStorage addLayoutManager:layoutManager];
[layoutManager release];

NSRect cFrame = NSMakeRect(0.0, 0.0, 8.5 * 72, 11.0 * 72);
NSTextContainer *container;
container = [[NSTextContainer alloc]
    initWithContainerSize:cFrame.size];
[layoutManager addTextContainer:container];
[container release];

// printView is an ivar, so you can use it later in printShowingPrintPanel: 
printView = [[NSTextView alloc] initWithFrame:cFrame textContainer:container];


----

**Here's an older example, that was formerly presented as the topic P**'rintFormatting**

I'm working on an app similar to a text-editor, which has a NSTextField containing the document's title, and a NSTextView containing the document's text. The trouble I'm having is when it comes to formatting them for printing. I want to have the title centered on the page, with bold type, then I want the document's text below it formatted as it is in the text view.

----

Create a new view that is the correct page size, add borders/etc and then start parsing your original text into your new view.

Here's some example code for a document based app:

    
- (IBAction)printDocument:(id)sender
{
    NSPrintOperation    *printOperation;
    
    // This will scale the view to fit the page without centering it.
    // It would be better to specify these default settings when
    // the document is created instead of in the print method.
    self printInfo] setHorizontalPagination:[[NSFitPagination];
    self printInfo] setHorizontallyCentered:NO];
    [[self printInfo] setVerticallyCentered:NO];
    
    // Setup the print operation with the print info and view
    printOperation = [[[NSPrintOperation printOperationWithView:[self printableView] printInfo:[self printInfo]];
    [printOperation runOperationModalForWindow:[self window] delegate:nil
            didRunSelector:NULL contextInfo:NULL];
}

- (NSView *)printableView
{
    NSTextView    *printView;
    NSDictionary    *titleAttr;

    
    // CREATE THE PRINT VIEW
    // 480 pixels wide seems like a good width for printing text
    printView = [[[NSTextView alloc] initWithFrame:NSMakeRect(0, 0, 480, 200)] autorelease];
    [printView setVerticallyResizable:YES];
    [printView setHorizontallyResizable:NO];

    // ADD THE TEXT
    // This assumes there is an NSTextField called titleField
    // and an NSTextView called mainTextView

    printView textStorage] beginEditing];

    // Set the attributes for the title
    titleAttr = [[[NSDictionary dictionaryWithObject:[NSFont boldSystemFontOfSize:14] forKey:NSFontAttributeName];
    
    // Add the title
    printView textStorage] appendAttributedString:[[[[[NSAttributedString alloc]
        initWithString:[titleField stringValue] attributes:titleAttr] autorelease]];
    
    // Create a couple returns between the title and the body
    printView textStorage] appendAttributedString:[[[[[NSAttributedString alloc] initWithString:@"\n\n"] autorelease]];
    
    // Add the body text
    printView textStorage] appendAttributedString:[mainTextView textStorage;
    
    // Center the title
    [printView setAlignment:NSCenterTextAlignment range:NSMakeRange(0, titleField stringValue] length])];
    
    [[printView textStorage] endEditing];
    
    // Resize the print view to fit the added text
    // (Is this done automatically?)
    [printView sizeToFit];
    
    return printView;
}


I haven't compiled this code so let me know if you come across any errors.

-- [[RyanBates

----

And that handles pagination correctly (sorry, I'm without my book which has printing)?

----

Although I have not tested that exact code snippet, a very similar code snippet seemed to handle pagination correctly. It will resize the view to fit horizontally on the page and place the view on the top left corner (not centering it) with the margin size at their default. These settings are done through NSPrintInfo. You may want to check out Apple's documentation on printing which has some more information on pagination:

http://developer.apple.com/documentation/Cocoa/Conceptual/Printing/index.html

-- RyanBates

----

One suggestion -- instead of assuming the width of 480 pixels, try this:

    
    printView = [[[NSTextView alloc] initWithFrame:self printInfo] imageablePageBounds autorelease];


This will make the allocated NSTextView the right width based on the actual size and orientation of the printable area of the page.  The only caveat is that [NSPrintInfo imageablePageBounds] is only available in 10.2 and later.

