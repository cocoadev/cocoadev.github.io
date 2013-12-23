---
layout: page
title: PDFKit
---

PDFKit (Tiger only) is Apple's framework for creating / editing PDFs. 

PDFKit Programming Guide:
http://developer.apple.com/documentation/GraphicsImaging/Conceptual/PDFKitGuide/index.html

PDFKit Objective-C Reference:
http://developer.apple.com/documentation/GraphicsImaging/Reference/PDFKit_Ref/index.html#//apple_ref/doc/uid/TP40001179

----

Not to be confused with this: http://www.pdfstore.com/details.asp?ProdID=685 ;) 

*Perish the thought! Okay, fine ... PDFKit.framework ... happy? ;-) *

----

After working with the framework for a while, I ran into a problem where using a constant key for the metadata attributes dictionary (returned from -documentAttributes) would cause an undefined symbol when linking. For instance, in this code:

    
- (id)initWithPDFDocument:(PDFDocument *)document;
{
	if ( ![super init] || !document )
		return nil;
	
	NSDictionary *PDFAttributes = [document documentAttributes];
	
	NSString *PDFTitle = [PDFAttributes objectForKey:PDFDocumentTitleAttribute];
	if ( !PDFTitle )
		PDFTitle = [document documentURL] absoluteString] lastPathComponent];
	
	title = [[[PDFTitle copy];
	
	return self;
}


...everything would compile fine, but PDFDocumentTitleAttribute would cause an undefined symbol in the linking build stage. Doing a google search I found a page in French that suggested this is a bug in the framework, rather than a problem with my project. Luckily for me, someone there also suggested these values for the dictionary constants:

    
PDFDocumentTitleAttribute --> @"Title"
PDFDocumentAuthorAttribute --> @"Author"
PDFDocumentSubjectAttribute --> @"Subject"
PDFDocumentCreatorAttribute --> @"Creator"
PDFDocumentProducerAttribute --> @"Producer"
PDFDocumentCreationDateAttribute --> @"CreationDate"
PDFDocumentModificationDateAttribute --> @"ModDate"
PDFDocumentKeywordsAttribute --> @"Keywords"


I haven't tested all of these, but the ones I have seem to be valid. Feel free to edit this out once Apple fixes the bug. :)

