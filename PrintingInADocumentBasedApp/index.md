---
layout: page
title: PrintingInADocumentBasedApp
---

I'm writing a visualization app for datasets resultant from a learning algorithm for my AI development -- because, frankly, I need to understand what my bots are *learning*, and looking at a hex-dump or post-order traversals on the command line aren't cutting it.

I'm curious about adding printing facilities. Also because if I have to trace out a really complex graph, it'll be easier on paper than on my meager 12" powerbook's screen. The trouble is, the app has two nibs. MainMenu.nib and ReflexDocument.nib ( renamed from MyDocument.nib ). The Print... menu item is in MainMenu.nib -- it needs to be connected to whatever's the current main window and print the visualization from there.

Right now, the print menu item is connected to FirstResponder.printDocument -- so I'm probably missing something but somehow I need to make the visualization view in the current main window be that FirstResponder.

Second problem: I've got a floating inspector panel which acts as a way of looking at and editing specific subsets of the data for the document window. I've handled this by having my NSDocument subclass register itself for window notifications and when the document's window becomes main, it tells the inspector panel to represent that window's document/model. This way, the inspector shows the data for the current document; I can have multiple documents open and the inspector shows/edits the data only for the topmost document window. This works fine. The trouble is, if I'm working in the inspector, and I hit command-w to close the current document, nothing happens. If I hit command-s, the save dialog opens, which is fine. If I hit command-n a new document opens, which is fine. But command-w does nothing unless the current document window is key. (For reference, the inspector isn't closable ). I'd like to have the command-w automatically close the current main document window without it necessarily being key. My guess is I'd have to have the inspector window catch the performClose message and forward that to the main window. Is this the right approach? It seems hacky to me.

I've written *lots* of single window cocoa apps, and I'm perfectly comfortable with the responder-chain and so on and so forth. But, document based apps seem to do a lot behind the curtain, and I'm not certain what hooks up with what. I do have a copy of O'Reilly's Learning Cocoa with Objective-C and it has the basics which I worked from.

Any suggestions?

--ShamylZakariya

----

For printing, you could implement     - (void)printShowingPrintPanel:(BOOL)flag in your document class. The default     printDocument: invokes this with a YES argument. Also make sure your document class is set as the window's delegate.

----

Here's a way to have command-W always close the current document instead of the main/key window:

1. In MainMenu.nib, create an action titled "closeDocument:" (or something similar) in the First Responder.

2. Link the "Close" menu item to the "closeDocument:" action in the First Responder.

3. In MyDocument.nib, create the "closeDocument:" action in the File's Owner (NSDocument subclass).

4. Add this code to your NSDocument subclass:

    
- (IBAction)closeDocument:(id)sender
{
	[self canCloseDocumentWithDelegate:self
			shouldCloseSelector:@selector(document:shouldClose:contextInfo:)
			contextInfo:NULL];
}

- (void)document:(NSDocument *)document shouldClose:(BOOL)shouldClose
			contextInfo:(void *)contextInfo
{
	if (shouldClose) {
		[self close];
	}
}


-- RyanBates

----

Thank you both -- both methods work.

I have one new question, however.

My document displays a complex graphic, which represents the data it represents. The graphic is made up of a ton of bezier paths and string drawing and so on. I keep track of the minimum rectangle that can hold the entire graphic, in pixels. I use this to resize the view and manage scrollbars. It works great -- on screen.

Now, I'm trying to print this graphic and I've got a problem. I need to make an NSAffineTransform to scale the graphic down so it fits on one page. I use the NSPrintInfo and get the imageablePageBounds and margin information, but it doesn't seem to work very well. E.g., the logical approach is something like this:

    

- (void)drawRect:(NSRect)rect
{
	if ( [NSGraphicsContext currentContextDrawingToScreen] ) 
	{
		NSRect bounds = [self bounds];

		[[NSColor whiteColor] set];
		NSRectFill( bounds );

		NSAffineTransform *scaler = [NSAffineTransform transform];
		[scaler scaleBy: scale]; //scale is set by command{+-}
		[scaler concat];

		[graph draw];	
		
		[scaler invert];
		[scaler concat];

	} else {
	
		NSPrintInfo *info = [NSPrintInfo sharedPrintInfo];
		
		NSRect pb = [info imageablePageBounds];
		NSRect er = [graph enclosingRect]; //smallest rect holding entire graph
		
		float xScale = (pb.size.width - pb.origin.x) / (er.origin.x + er.size.width);
		float yScale = (pb.size.height - pb.origin.y ) / (er.origin.y + er.size.height);
		float minScale = xScale < yScale ? xScale : yScale;

//		NSLog( @"origin: (%f, %f) size: (%f, %f)", 
//			pb.origin.x, pb.origin.y, pb.size.width, pb.size.height );
			
		NSAffineTransform *scaler = [NSAffineTransform transform];
		[scaler scaleBy: minScale];
		[scaler translateXBy: -er.origin.x yBy: -er.origin.y];
		[scaler concat];

		[graph draw];	
		
		[scaler invert];
		[scaler concat];

	}	
}



In principle, this should create a scaling transform that would fit the entire graph onto the imageable area for the printer. What happens, however, is weird. First, the right-hand quarter or so of the graph is cut off. Second, the print system prints THREE pages, the first has the cropped image, the second two are blank!

I tried shrinking the scaling transform further so the graph would definately fit on one page, and even then, the print system spooled three pages, again with the second and third blank.

I figured out how to override NSView's automatic pagination, by implementing      -(BOOL) knowsPageRange: (NSRangePointer) range  as well as      -(NSRect) rectForPage: (int) pageNumber  however, I'm still having scaling issues.

If I do something as straightforward as stroking the bezier path returned by NSPrintInfo's imageablePageBounds the rect is half off-page.

----

I've solved it, and I hope whatever I've figured out might actually be of use to somebody, someday.

First, since I'm printing out a node graph type image, I decided that I could just shrink-to-fit on one page, so for my graphing view I implemented:

    

- (BOOL) knowsPageRange: (NSRangePointer) range
{
    range->location = 1;
    range->length = 1;

    return YES;
}



And, for that one page, I need to specify a rect to draw into:

    

- (NSRect ) rectForPage: (int) pageNumber
{
    NSPrintInfo *info = [NSPrintInfo sharedPrintInfo];
    return [info imageablePageBounds];
}



Subsequently, in my      -(void) drawRect: (NSRect ) rect  method, all I have to do is create an NSAffineTransform and use it to offset my coorindate system by     rect.origin.x and     rect.origin.y and then set up a subsequent NSAffineTransform to scale my coordinate system to fit my node graph into     rect.size.width ( or     rect.size.height ) depending on wether my graph is taller or wider.

Anyway, it works!

--ShamylZakariya

----

Unless you need to do more than just scale, and need to actually change what you draw depending on the size, you could simply get the draw view in eps or pdf, and scale the static image, instead of the view, before sending it out to the printer view; you could also split the image across multiple pages using the statical image.  This is can make sense for a number of reasons: first, you don't want print code mixing with your draw code for performance reasons, and second, manipulating a static image is just a few lines of code :).

