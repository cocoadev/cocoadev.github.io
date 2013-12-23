---
layout: page
title: EditingPDFMetadata
---



I'm hoping someone can give me a bit of advice. I've been working on an application to edit the metadata in existing PDF files. By combining and adding to the ideas in the PDFKitView and PDFLinker2 applications, I have the PDF view, outline view, and search functions working beautifully.

I can display the metadata in an existing file, but I'm having trouble getting all of the attributes to save.

The attributes: "Title", "Author", and "Creator" all work fine. I can change them, save the file, and those changes are there when the file is reopened.

If I open an existing PDF file that has a "Subject" attribute, I can display it, but it always disappears when the file is saved. If I add any new attributes, they disappear as well. (There's also a "Producer" attribute that always gets changed to "Mac OS X 10.4.9 Quartz PDFContext", but that's fine.)

Within my windowControllerDidLoadNib method, the following code loads the PDF document and copies the document Attrbutes to an NSMutableDictionary:

     
	// Load PDF.
	if ([self fileName])
	{
	  if (pdfDocument != NULL) [pdfDocument release];
	  pdfDocument = [[PDFDocument alloc] initWithURL: [NSURL fileURLWithPath: [self fileName]]];
	  [_pdfView setDocument: pdfDocument];

	  if (pdfAttributes != NULL) [pdfAttributes release];
	  pdfAttributes = [[NSMutableDictionary dictionaryWithCapacity: 10] retain];
	  [pdfAttributes setDictionary:pdfDocument documentAttributes] copy;
	  [pdfAttributesTable reloadData];
	}
 

In my interface, a TableView (pdfAttributesTable) correctly displays all the attributes.

There are text fields for editing the various fields, which when changed, update specific items in the pdfAttributes MutableDictionary and uses setDocumentAttributes to update the pdfDocument itself. Finally, the pdfAttributesTable reloads again, so I can verify that the changes are there before the file is saved:

     
- (void) fieldChanged:(id)theField :(NSString *)theKey 
{
	NSString *theText;
	theText = [theField stringValue];

	if ([theText length] > 0) [pdfAttributes setValue:theText forKey:theKey ];
	else [pdfAttributes setValue:nil forKey:theKey ];
	[pdfDocument setDocumentAttributes:pdfAttributes];

	[pdfAttributesTable reloadData];
}
 

I started out by directly editing _pdfView document] documentAttributes], but when that didn't work, I tried using my own pdfDocument and pdfAttributes objects in case the [[PDFView was somehow eating the "Subject" attribute. (Unfortunately, it didn't make any difference, but of course the former way was much simpler.)

How can I get the "Subject" attribute to take? And, how can I add my own additional custom attributes to the PDF metadata?

-- Elmer   (I've found a lot of great information on this site. Thanks in advance for any help!)

