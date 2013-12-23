---
layout: page
title: ReadingPDFPageDimensions
---

The following code illustrates an example showing how the printing page dimensions of a PDF file can be extracted from an existing PDF document that is stored on the disk.  This code assumes that the path to the file to be interpreted is passed to the following method as an instance of NSURL.  Also note that this method assumes that the user is only interested in the dimensions of the first page of the PDF file - if page dimensions of the other pages in the file are required then it will be necessary to amend the code to iterate through the pages of the PDFDocument instance and return the size of the pages in a collection, such an NSArray containing the NSSize structs.

    
- (NSSize)	sizeOfPDFDocument:(NSURL *) documentURL {
	
	// Declare size struct to return
	NSSize documentSize;
	
	// Get the PDFDocument
	PDFDocument *myDocument = [[PDFDocument alloc] initWithURL:documentURL];
	
	// Get the pixel dimensions of the first page of the PDF document
	PDFPage *firstPage = [myDocument pageAtIndex:0];
	NSRect bounds = [firstPage boundsForBox:kPDFDisplayBoxMediaBox];
	NSSize pixelSize = bounds.size;
	
	// Convert the pixel dimensions to inches and return
	documentSize.width = pixelSize.width / 72;
	documentSize.height = pixelSize.height / 72;
	
	// Release objects
	[myDocument release];
	
	return documentSize;
}


Please also note that the above comes from a (very) novice programmer so beware - however, it works for me.

----

Good stuff. I hope you don't mind but I've added this functionality to PDFDocumentCategory and made it a bit more generic in the process. :)

----

Cool, except that this will probably break in Leopard due to resolution independence.  Also, since you claim to be a novice programmer, I feel I should ask, why have you written an instance method that doesn't operate on an object, but rather a static URL provided in a parameter?  (Rhetorical question intended to be thought-provoking).  As per the above, you should really research the Cocoa concept of categories, and the concept of OOP in general.

If your code is going to be operating on a specific PDF document, then why not make it a member of the PDFDocument class?  In Cocoa, this can easily be done through categories.  You would create a category on PDFDocument that included your instance method (sans the one and only parameter).  It would then operate on "self", and avoid the entire URL lookup altogether.  Think about it for a second, the programmer will always be able to get a PDFDocument for a PDF that he/she wants the dimensions of.  But he/she may not always be able to get a URL (what if my document-based application works on PDFDocuments?).

Also, a stylistic issue: the comments are completely unnecessary, except for the one that precedes the division by 72.  Everything else should be self-explanatory.  If you find yourself writing comments to explain declarations or assignments, either you're commenting too much, or your code is too confusing and needs to be refactored.  Explaining magic constants like 72 is why comments exist; we have symbolic variable names so we don't have to go around saying "return_value is the value we're going to return".

Just some food for thought to help you on your way to being a better Cocoa developer.  Great code, though.

----

I don't see why this would break on Leopard since the bounds returned for PDF display boxes are in page space which is defined by the PDF file format, and is not going to change due to resolution independence on any particular implementation. This would only break if the definition of the PDF page space were to change. It may be less confusing though to use "pointSize" rather than "pixelSize" for the variable name, since you're referring to PDF page space points, rather than to screen pixels.

