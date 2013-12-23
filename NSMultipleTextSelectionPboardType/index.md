---
layout: page
title: NSMultipleTextSelectionPboardType
---

NSMultipleTextSelectionPboardType is a standard pasteboard type that's a little different from the norm.  The documentation about it is a little hard to find; the only documentation I've seen are in the Leopard (10.5) AppKit release notes, which says the following:

----
Finally, there is a new pasteboard type used by NSTextView when copying and pasting multiple selections.

    NSString *NSMultipleTextSelectionPboardType;

This type is used only when the pasteboard is representing a multiple selection. The contents for this type should be an array of NSNumbers, one for each subrange of the selection, indicating the number of paragraphs contained in each subrange. The plain or rich text contents of the pasteboard will be a string representing the contents of each subrange concatenated with paragraph breaks in between them (where they do not already end in paragraph breaks); that combined with the paragraph counts in the NSMultipleTextSelectionPboardType is sufficient to determine which portions of the contents are associated with which subrange. This mechanism has been chosen because it is consistent across plain and rich text, and across different representations of rich text. The counts may be checked for consistency by comparing the total number of paragraphs in the plain or rich text contents of the pasteboard with the total of the numbers in the NSMultipleTextSelectionPboardType array; if the two do not match, then the NSMultipleTextSelectionPboardType contents should be ignored.
----

(Thanks to Douglas Davidson for a pointer to the documentation.)

One more unusual thing that the documentation doesn't mention is that data on the NSPasteboard for the array of NSNumbers is an (XML!) property list, not an NSArchived array as is usual for most pasteboard data.  

Since the pasteboard data's in a rather unusual format and is pretty annoying to deal with, here's some code to extract the text selections off the pasteboard for the NSMultipleTextSelectionPboardType.  BSD-licensed, etc etc.  It currently extracts the text in plaintext format, but it should be fairly easy to support rich text.

    
/// Returns an NSArray, where each item in the array is a single plain-text NSString representing a single selection from the pasteboard.  Returns nil if the pasteboard doesn't currently hold an NSMultipleTextSelectionPboardType, or if the total number of paragraphs in the pasteboard text do not match the total of the nubmes in the NSMultipleTextSelectionPboardType array (as per the documentation).
/** See http://developer.apple.com/releasenotes/Cocoa/AppKit.html (search for NSMultipleTextSelectionPboardType) for information on the pasteboard data format. */
NSArray* NSPasteboardPlainTextArrayForMultipleTextSelection(NSPasteboard* pboard)
{
	NSString* firstAvailablePasteBoardType = [pboard availableTypeFromArray:[NSArray arrayWithObject:NSMultipleTextSelectionPboardType]];
	if(![firstAvailablePasteBoardType isEqual:NSMultipleTextSelectionPboardType]) return nil;
	
	id propertyList = [pboard propertyListForType:NSMultipleTextSelectionPboardType];
	NSArray* numberOfParagraphsPerSelection = [propertyList isKindOfClass:[NSArray class]] ? (NSArray*)propertyList : nil;
	if(numberOfParagraphsPerSelection == nil) return nil;
	
	NSString* allPlainTextSelections = [pboard stringForType:NSStringPboardType];
	
	static NSCharacterSet* paragraphSeparatorCharacterSet = nil;
	if(paragraphSeparatorCharacterSet == nil)
	{
		// We can't use [NSCharacterSet newlineCharacterSet] here, since that covers U+000A-U+000D and U+0085; we only want U+000A
		paragraphSeparatorCharacterSet = [[NSCharacterSet characterSetWithRange:NSMakeRange('\n', 1)] retain];
	}
	
	NSMutableArray* textSelections = [NSMutableArray array];
	
	NSUInteger numberOfParagraphsScanned = 0;
	NSScanner* paragraphScanner = [NSScanner scannerWithString:allPlainTextSelections];
	for(NSNumber* paragraphsToScanNumber in numberOfParagraphsPerSelection)
	{
		const NSUInteger numberOfParagraphsToScan = [paragraphsToScanNumber unsignedIntegerValue];
		
		NSMutableString* nextTextSelection = [NSMutableString string];
		for(NSUInteger i = 0; i < numberOfParagraphsToScan; i++)
		{
			NSString* nextParagraph = nil;
			
			const BOOL didScanOK = [paragraphScanner scanUpToCharactersFromSet:paragraphSeparatorCharacterSet intoString:&nextParagraph];
			if(didScanOK) numberOfParagraphsScanned++;
			else break;
			
			[nextTextSelection appendString:nextParagraph];
			
			if(i+1 != numberOfParagraphsToScan) [nextTextSelection appendString:@"\n"];
		}
		
		[textSelections addObject:nextTextSelection];
	}

	NSUInteger totalNumberOfParagraphs = 0;
	for(NSNumber* numberOfParagraphsInSingleSelectionNumber in numberOfParagraphsPerSelection)
	{
		totalNumberOfParagraphs += [numberOfParagraphsInSingleSelectionNumber unsignedIntegerValue];
	}

	if(numberOfParagraphsScanned == totalNumberOfParagraphs) return textSelections;
	else return nil;
}


And, here's some code to write data to the pasteboard.  You probably want to use the     NSPasteboardSetDataForPlainTextSelections() function below, which writes directly to the pasteboard for you.

    
/// Given an NSArray of NSString items, this creates NSData and NSString objects that is intended to be written to an NSPasteboard for NSMultipleTextSelectionPboardTypeData.  Returns YES on success; NO otherwise.
/** See http://developer.apple.com/releasenotes/Cocoa/AppKit.html (search for NSMultipleTextSelectionPboardType) for information on the pasteboard data format. */
BOOL NSPasteboardDataForPlainTextArray(NSArray* textSelections, NSData** outNSMultipleTextSelectionPboardTypeData, NSString** outNSStringPboardTypeString)
{
	if(outNSMultipleTextSelectionPboardTypeData == nil) return NO;
	if(outNSStringPboardTypeString == nil) return NO;
	
	static NSCharacterSet* paragraphSeparatorCharacterSet = nil;
	if(paragraphSeparatorCharacterSet == nil)
	{
		// We can't use [NSCharacterSet newlineCharacterSet] here, since that covers U+000A-U+000D and U+0085; we only want U+000A
		paragraphSeparatorCharacterSet = [[NSCharacterSet characterSetWithRange:NSMakeRange('\n', 1)] retain];
	}
	
	NSMutableArray* numberOfParagraphs = [NSMutableArray array];
	for(NSString* textSelection in textSelections)
	{
		NSArray* paragraphs = [textSelection componentsSeparatedByCharactersInSet:paragraphSeparatorCharacterSet];
		
		[numberOfParagraphs addObject:[NSNumber numberWithUnsignedInteger:[paragraphs count]]];
	}
	
	NSString* errorString = nil;
	NSData* NSMultipleTextSelectionPboardTypeData = [NSPropertyListSerialization dataFromPropertyList:numberOfParagraphs format:NSPropertyListXMLFormat_v1_0 errorDescription:&errorString];
	if(NSMultipleTextSelectionPboardTypeData)
	{
		*outNSMultipleTextSelectionPboardTypeData = NSMultipleTextSelectionPboardTypeData;
	}
	else
	{
		NSLog(@"Couldn't construct NSMultipleTextSelectionPboardType property list from array: %@", errorString);
		
		return NO;
	}

	*outNSStringPboardTypeString = [textSelections componentsJoinedByString:@"\n"];
	
	return YES;
}

/// Writes the given NSArray of NSString items to the given pasteboard. Note that you must declare that NSMultipleTextSelectionPboardType is to be written to the pasteboard by yourself, using [pboard declareTypes:[NSArray arrayWithObject:NSMultipleTextSelectionPboardType] owner:yourClass]: this function cannot do it because it doesn't know who the know is.  Returns YES if successful; NO otherwise.
/** See http://developer.apple.com/releasenotes/Cocoa/AppKit.html (search for NSMultipleTextSelectionPboardType) for information on the pasteboard data format. */
BOOL NSPasteboardSetDataForPlainTextSelections(NSArray* textSelections, NSPasteboard* pboard)
{
	NSData* NSMultipleTextSelectionPboardTypeData = nil;
	NSString* NSStringPboardTypeString = nil;
	
	const BOOL didCreateDataForPasteboardOK = NSPasteboardDataForPlainTextArray(textSelections, &NSMultipleTextSelectionPboardTypeData, &NSStringPboardTypeString);
	if(!didCreateDataForPasteboardOK) return NO;
	
	const BOOL setMultipleTextSelectionOK = [pboard setData:NSMultipleTextSelectionPboardTypeData forType:NSMultipleTextSelectionPboardType];
	if(!setMultipleTextSelectionOK) return NO;
	
	const BOOL setStringOK = [pboard setString:NSStringPboardTypeString forType:NSStringPboardType];
	if(!setStringOK) return NO;
	
	return YES;
}


All the code above is tested, and works OK for me.  Hopefully this will save people some trouble.  -AndrePang

