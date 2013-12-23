---
layout: page
title: ScalingImagesWhenPriting
---

The following code takes a pdf file, creates a NSPDFImageRep from it, stores it in a series of subviews of a NSImageView, then displays the grid within that view.  The screen image scales ok, and when printing, the mainView is scaled ok, but the scaling code doesn't seem to work at all when printing within the subviews.  Mike Trent suggested I needed to subclass my own NSView instead of creating all of the subviews, but I'm not sure I know how.  

Can someone see why the image isn't scaling while printing?  The whole purpose is to create a singled page pdf file from a multi-page pdf file.  Any pointers would be appreciated, especiallly if you can explain why it's not scaling the subviews during printing.

----

I think Mike's suggestion to subclass is the way to go. Cocoa is going to resize the main view for the purpose of printing, and you are not hooked into any of the drawing routines that create the output. If you subclass a view, you can override the     drawRect: method and tile the image reps accordingly. 

On a side not, you might want to brush up on your MemoryManagement techniques because I noticed a couple of leaks in your example. Two leaks I spotted when briefly looking over you code are easy to correct. The first leak is the blind assingment of an instance variable without checking to see if the variable is already assigned to an existing object.     data is being assigned the reference to a data object without checking to see if     data was pointing to an object allocated from a previous allocation. You should do the following in an assignment like this:

    
    if (data) [data release];
    data = [[NSData dataWithContentsOfFile:fileName] retain];


The other leak is similar to the previous case.     theImage is being assigned to an object without checking to see if the variable is pointing to something already.

----

I do need to brush up on MemoryManagement.  But, I still have the basic problem -- anyone have any pointers, or a trivial code example?  

(I'm not sure I realize why subclassing is so much better than creating subviews, but that's not really my question -- good architecture or not, shouldn't this work?  Is there a bug in the API?  Am I missing something?)

----

I'm still hoping someone can help me, but meanwhile I've been looking at doing my drawing in a drawRect: of a NSImageView subclass, but I'm not sure I really understand -- NSImageView holds a single image.  I can use a custom NSImageView subclass (I will call it PDFImageView) to contain a given page of a pdf.  But, at some point, I will have to make a separate subview of some containing view for each of the separate images, wouldn't I?  The only way I know how to display a page of a pdf file is with a NSPDFImageRep, which I add to an NSImage and set in an NSImageView.  Can someone show me a trivial code example?  

What I'm trying to do should not be all that complicated -- I'm trying to lay out a single page pdf with an optimal grid showing the entire multi-page pdf on a single page (sort of a contact sheet of sorts -- it could also be useful for displaying multiple images in a folder, I suppose).

Thanks,
Darin

----

    
#import "Controller.h"

@implementation Controller

- (void)awakeFromNib
{
	[self openFile:self];	
}

- (void)calcgrid:(int)pages
{
	//temporary kludge for 18 page test pdf until I deal with grid optimization routine for page
	if (pages<25){
		xcount=5;
		ycount=4;
	}
	else {
		xcount=6;
		ycount=6;
	}
	return;
}

- (void)openFile:(id)sender
{
    NSOpenPanel *panel = [NSOpenPanel openPanel];
    if ([panel runModal] == NSOKButton) {
        NSString *fileName = [panel filename];
		data = [[NSData dataWithContentsOfFile:fileName] retain];
		NSPDFImageRep *tempPDFRep = [NSPDFImageRep imageRepWithData:data];	
		pgw=[tempPDFRep bounds].size.width;
		NSLog(@"pgw %f:",pgw);
		pgh=[tempPDFRep bounds].size.height;
		NSLog(@"pgh %f:",pgh);
		[self calcgrid:[tempPDFRep pageCount]];
		[self redraw:self];
	}
}

- (IBAction)redraw:(id)sender
{
	int i,j;
	int pg,num;
	pg=0;
//	NSURL *theURL = [NSURL URLWithString:@"/Users/darin/Documents/patents/pat4959755.pdf"];
//	NSImage *theImage = [[NSImage alloc]initByReferencingURL:theURL];
	for (j=0;j<ycount;j++){
		for (i=0;i<xcount;i++){

			//create re
			thePDFRep = [[NSPDFImageRep imageRepWithData:data] retain];	
			num=[thePDFRep pageCount];
			[thePDFRep setCurrentPage:pg];

			//create an image, and add rep
			theImage = [[NSImage alloc]init];
			[theImage addRepresentation:thePDFRep];
			[theImage setDataRetained:YES];

			//create new subview, and add image, then redraw
			NSRect rect = NSMakeRect(i*pgw,j*pgh,pgw,pgh);
			NSImageView *newView = [[NSImageView alloc] init];
			[mainView setImageScaling:NSScaleProportionally];
			[newView setImageScaling:NSScaleProportionally];
			[newView setImageFrameStyle:NSImageFrameGroove];			
			[newView setFrame:rect];
			[mainView setImageAlignment:NSImageAlignBottomLeft];
			[mainView setImageFrameStyle:NSImageFrameGroove];
			[newView setImage:theImage];
			[newView setNeedsDisplay];
			[mainView addSubview:newView];
			NSRect mainRect=NSMakeRect(0,0,pgw*xcount,pgh*ycount);
			[mainView setFrame:mainRect];
			[mainView setNeedsDisplay];

			pg++;
			if (pg>=num) j=ycount;
		}
			if (pg>=num) i=xcount;
	}
	//get rid of data since we're done now
	[data release];
}

- (IBAction) print: (id) sender
{		
	NSPrintInfo *printInfo;
	NSPrintOperation *printOp;
	printInfo = [NSPrintInfo sharedPrintInfo];
	[printInfo setHorizontalPagination: NSFitPagination];
	[printInfo setVerticalPagination: NSFitPagination];
	[printInfo setVerticallyCentered:NO];
	printOp = [NSPrintOperation printOperationWithView:mainView printInfo:printInfo];
	[printOp setShowPanels:YES];
	[printOp runOperation];
}
@end

