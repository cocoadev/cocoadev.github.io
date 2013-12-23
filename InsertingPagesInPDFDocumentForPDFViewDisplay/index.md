---
layout: page
title: InsertingPagesInPDFDocumentForPDFViewDisplay
---



I'm trying to find a better way to insert numerous pages into a PDFDocument for display in a PDFView.

I have an array containing PDFPages I'm calling "_pages".

I've tried the intuitive approach, but the PDFView is screwing it up...it won't display the thing, for some reason if I change the [_pages objectAtIndex:i] index value to a static number it will work (putting the same page in throughout the whole script), but otherwise there are always problems, either at the end of the document where the PDFView goes berserk (indexForPage:page not found). Sometimes it puts in a default small page. I've looked pretty extensively into my _pages array and counters and they appear to be alright, so I think it's a problem with my understanding of PDFView and/or PDFDocument rather than a pointer error. (Also, others have had this problem)

    

	int i;

	PDFDocument *masterScriptDocument = [[PDFDocument alloc] initWithData:[_pages objectAtIndex:0] dataRepresentation]];
	for (i=1;i<_numberOfPages; i++) {
		[masterScriptDocument insertPage:[_pages objectAtIndex:i] atIndex:i];
	}




I've looked around online and found this solution:

    

	int i;

	PDFDocument *masterScriptDocument = [[PDFDocument alloc] initWithData:_pages objectAtIndex:0] dataRepresentation;
	for (i=1;i<_numberOfPages; i++) {
		PDFDocument *aPage = [[PDFDocument alloc] initWithData:_pages objectAtIndex:0]  dataRepresentation;
		[masterScriptDocument insertPage:[aPage pageAtIndex:0] atIndex:i];
		masterScriptDocument = [[PDFDocument alloc] initWithData:[masterScriptDocument dataRepresentation]]; 

	}


This works, but freezes up my computer on anything over twenty pages.

Anybody have a fix for this?

Thanks - EricBadros
----

I've figured it out and though it is reasonably slow (it takes about 3-4 times as long as preparing the document for printing) it works consistently and correctly...I'm going to try out a few more things to speed it up (and make it more elegant) before posting my solution. - EricBadros

----
Sorry, almost forgot that I had promised to post this.


I have two NSTableViews subclass instances, one with the revision document and one with the source document. One can choose pages from the source document and then choose which pages to replace, or insert in the revision document...that's why you may see a lot of extraneous stuff that isn't vitally important but provides a more complete solution (I think). This is in my ScriptManagerController. Please forgive me, I made an assumption that both documents pages are 612x792...easily fixed but gets the point across.

    

- (void) insertPages {
	int j, insertionIndexOfDestination, indexOfRevision;
	//Make a copy of the revision that is being modified
	indexOfRevision = [_scriptRevisionPopUpButton indexOfSelectedItem];
	
	[self setNewRevision:_projectData revisions] objectForKey:[[_projectData revisionTitles] objectAtIndex:indexOfRevision];
	//	[self setNewRevision:_projectData revisions] objectAtIndex:indexOfRevision;

	// Find and store the selected source pages in NSMutableArray pagesToBeInserted
	int i;
	NSMutableArray *pagesToBeInserted = [[NSMutableArray alloc] init];
	for (i= 0; i<= [_scriptTableView numberOfRows]; i++) {
		if ([_scriptTableView isRowSelected:i] == YES) {
			NSMutableDictionary *pageInformation = [[NSMutableDictionary alloc] init];
			[pageInformation setObject:[_scriptPopUpButton titleOfSelectedItem] forKey:@"fromScriptNamed"];
			[pageInformation setObject:[NSNumber numberWithInt:i] forKey:@"fromScriptPage"];
			PDFDocument *theSourcePDF = [[PDFDocument alloc] initWithData:[_projectData scripts] objectForKey:[_scriptPopUpButton titleOfSelectedItem objectForKey:@"document"]];
			[pageInformation setObject:theSourcePDF pageAtIndex:i] dataRepresentation] forKey:@"page"];
			[pagesToBeInserted addObject:pageInformation];
			[pageInformation release];
		}
	}
	
	insertionIndexOfDestination = [[_scriptRevisionTableView selectedRowIndexes] firstIndex];
	
	[[NSMutableArray *newRevisionPages = [[NSMutableArray alloc] init];
	newRevisionPages = _newRevision pages] mutableCopy];
	for (j=0; j<[pagesToBeInserted count] ; j++) {
		[newRevisionPages insertObject:[pagesToBeInserted objectAtIndex:j] atIndex:insertionIndexOfDestination];
		[self insertComponentValuesForPage:insertionIndexOfDestination];
		[self insertAnnotationsForPage:insertionIndexOfDestination];
		insertionIndexOfDestination = insertionIndexOfDestination + 1;
	}
	[pagesToBeInserted release];
	[_newRevision setPages:newRevisionPages];
	[[NSString *revisionName = [NSString stringWithFormat:@"Revision #%i", (_projectData revisions] count])];
	//[self createScriptForRevision:_newRevision withName:revisionName];
	[_projectData addRevision:_newRevision withName:revisionName];
	[_newRevision release];
	[self prepareWindow];
	[self closeWaitWindow];

}


    

- (void) replacePages {
	int  j, insertionIndexOfDestination, indexOfRevision;
	
	//Make a copy of the revision that is being modified
	indexOfRevision = [_scriptRevisionPopUpButton indexOfSelectedItem];
	
	[self setNewRevision:[[_projectData revisions] objectForKey:[[_projectData revisionTitles] objectAtIndex:indexOfRevision];

	//[self setNewRevision:_projectData revisions] objectAtIndex:indexOfRevision;
	
	// Find and store the selected source pages in NSMutableArray replacementPages
	int i;
	NSMutableArray *replacementPages = [[NSMutableArray alloc] init];
	PDFDocument *theSourcePDF = [[PDFDocument alloc] initWithData:[_projectData scripts] objectForKey:[_scriptPopUpButton titleOfSelectedItem objectForKey:@"document"]];
	NSString *theSourceScriptName = [_scriptPopUpButton titleOfSelectedItem];
	for (i= 0; i< [_scriptTableView numberOfRows]; i++) {
		if ([_scriptTableView isRowSelected:i] == YES) {
			NSMutableDictionary *pageInformation = [[NSMutableDictionary alloc] init];
			[pageInformation setObject:theSourceScriptName forKey:@"fromScriptNamed"];
			[pageInformation setObject:[NSNumber numberWithInt:i] forKey:@"fromScriptPage"];
			[pageInformation setObject:[theSourcePDF pageAtIndex:i] dataRepresentation] mutableCopy] forKey:@"page"];
			[replacementPages addObject:pageInformation];
			[pageInformation release];
		}
	}
	
	
	// Copy the current revision and replace the pages into the copy, also change the name and number of pages dictionary objects
	insertionIndexOfDestination = [[_scriptRevisionTableView selectedRowIndexes] firstIndex];
	int lastIndexOfDestination = [[_scriptRevisionTableView selectedRowIndexes] lastIndex];
	
	[[NSMutableArray *newRevisionPages = [[NSMutableArray alloc] init];
	newRevisionPages = _newRevision pages] mutableCopy];
	j = 0;
	for (j=insertionIndexOfDestination; j<= lastIndexOfDestination; j++) {
		[newRevisionPages removeObjectAtIndex:insertionIndexOfDestination];
		[self removeComponentValuesForPage:insertionIndexOfDestination];
		[self removeAnnotationsForPage:insertionIndexOfDestination];

	}
	
	int k;
	for (k = 0; k<[replacementPages count]; k++) {
		[newRevisionPages insertObject:[replacementPages objectAtIndex:k] atIndex:insertionIndexOfDestination];
		[self insertComponentValuesForPage:insertionIndexOfDestination];
		[self insertAnnotationsForPage:insertionIndexOfDestination];
		insertionIndexOfDestination = insertionIndexOfDestination + 1;
	}
	
	[replacementPages release];
	[_newRevision setPages:[newRevisionPages mutableCopy;
	[newRevisionPages release];
	NSString *revisionName = [NSString stringWithFormat:@"Revision #%i", (_projectData revisions] count])];
	[[PDFDocument *theScript = [self createScriptForRevision:_newRevision withName:revisionName];
	[_newRevision setScript:[theScript dataRepresentation]];
	[_projectData addRevision:_newRevision withName:revisionName];
	[_newRevision release];
	[self prepareWindow];
	[self closeWaitWindow];
}


    
- (NSData *) createScriptForRevision:(Revision *) aRevision withName:(NSString *) aName{
	[_scriptCreatorView drawPdfWithPages:[aRevision pages]];
	[_scriptCreatorView setNeedsDisplay:YES];
	NSPrintInfo *printInfo;
	NSPrintInfo *sharedInfo;
	NSPrintOperation *printOp;
	NSMutableDictionary *printInfoDict;
	NSMutableDictionary *sharedDict;
	
	sharedInfo = [NSPrintInfo sharedPrintInfo];
	sharedDict = [sharedInfo dictionary];
	printInfoDict = [NSMutableDictionary dictionaryWithDictionary:sharedDict];
	
	[printInfoDict setObject:NSPrintSaveJob forKey:NSPrintJobDisposition];
	NSString *revisionScriptFilePath = [NSString stringWithFormat:@"/%@.pdf", aName];

	[printInfoDict setObject:revisionScriptFilePath forKey:NSPrintSavePath];
	printInfo = [[NSPrintInfo alloc] initWithDictionary: printInfoDict];
	printOp = [NSPrintOperation printOperationWithView:[self scriptCreatorView] printInfo:printInfo];
	[printOp setShowPanels:NO];
	[printOp runOperation];
	NSURL *aPath;
	aPath = [NSURL fileURLWithPath:revisionScriptFilePath];
	PDFDocument *newMasterScript = [[[PDFDocument alloc] initWithURL:aPath] autorelease];
	return newMasterScript;

}



Then, the I have subclassed NSView for the ScriptCreatorView and here is the relevant code:

    

- (void) drawPdfWithPages:(NSMutableArray *) somePages {
	int i;
	_numberOfPages = [somePages count];
	_pdfPageImageArray = [[NSMutableArray alloc] init];
	for (i=0; i<_numberOfPages; i++) {
		NSImage *masterPdfImage = [[NSImage alloc] initWithSize:NSMakeSize(612, 792)];
		[masterPdfImage setDataRetained:YES];
		NSPDFImageRep *aPdfRep = [NSPDFImageRep imageRepWithData:somePages objectAtIndex:i] objectForKey:@"page";
		NSRect onePageBounds = [aPdfRep bounds];
		[masterPdfImage addRepresentation:aPdfRep];
		[masterPdfImage lockFocus];
		[[NSColor whiteColor] set];
		[NSBezierPath fillRect:onePageBounds];
		[aPdfRep drawInRect:onePageBounds];
		[masterPdfImage unlockFocus];
		[_pdfPageImageArray addObject:masterPdfImage];
		[masterPdfImage release];
	}
	NSRect frameRect;
	frameRect = NSMakeRect(0,0,612, 792);
	frameRect.size.height *= _numberOfPages;
	[super setFrame:frameRect];
}

    

- (void) drawRect: (NSRect) rect {
	int i;
	NSRect onePageBounds = NSMakeRect(0,0,612,792);
	NSAffineTransform* xform = [NSAffineTransform transform];
	NSRect frameRect = [self frame];
	[xform translateXBy:0.0 yBy:frameRect.size.height];
	[xform scaleXBy:1.0 yBy:-1.0];
	[xform concat];
	
	for (i=0; i< _numberOfPages; i++) {
		{
			onePageBounds.origin.y = frameRect.size.height - ((i+1) * onePageBounds.size.height);
			_pdfPageImageArray objectAtIndex:i] drawAtPoint:onePageBounds.origin fromRect:[[NSZeroRect operation:NSCompositeSourceOver fraction:1.0];
		}
	}
}


    

- (BOOL) isFlipped {
	return YES;
}


- (BOOL) knowsPageRange: (NSRangePointer) range
{
    range->location = 0;        // page numbers are one-based
    range->length = _numberOfPages + 1;  
	
    return YES;
}

- (NSRect) rectForPage: (int) pageNumber {
	NSRect pageRect = NSMakeRect(0,0,612, 792);
	pageRect.origin.y = (pageNumber - 1) * pageRect.size.height;
	return pageRect;
}



Hope that helps...I know a lot of people have been trying to make this work and I've found this is pretty fast...for 120 pages on a Powerbook G4 1.6 with 1GB I just get the pinwheel before the new PDFDocument is created. EricBadros


----
Maybe I'm misunderstanding the problem but why not do it like so?...

    
- (PDFDocument *)pdfDocumentFromArray:(id)array
{
	PDFDocument *masterDocument;
       NSEnumerator *enumerator = [array objectEnumerator];
		id anObject;
	
			while (anObject = [enumerator nextObject]) {
                             if (!masterDocument)
                                   masterDocument = [[PDFDocument alloc] initWithData:[anObject dataRepresentation]];
                             else
	                     	[masterDocument insertPage:anObject atIndex:[masterDocument pageCount]];
                    }

return [masterDocument autorelease];
}


That's basically the way I do it. As long as everything in the array is a PDFPage it should work ok. If I really need to insert pages in specific places (instead of just adding to the end) then I modify/re-order the array before making the document.

*I went ahead and turned that into PDFDocumentCategory so..yeah.*

