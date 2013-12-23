---
layout: page
title: NSTableViewFileDrag
---



I'm having a problem with dragging an NSTableView row to the desktop to create a file... I have some code that works, but after a drag happens, it requires a mouse down before it becomes responsive.

This is a problem.

I am using the following custom code to get the file drag:
    - (BOOL)tableView:(NSTableView *)aTableView 
        writeRows:(NSArray *)rows 
	 toPasteboard:(NSPasteboard *)uselessPBoard;
{
		// Make sure the row is selected before the drag begins.
		unsigned row = rows objectAtIndex:0] unsignedIntValue];
		[recipiesTableView selectRowIndexes:[[[NSIndexSet indexSetWithIndex:row]
					   byExtendingSelection:NO];
		[self selectedRecipeChanged:aTableView];
		
		
		NSImage * dragImage = nil;
		NSPoint dragPosition = NSZeroPoint;
		NSPasteboard * pboard = [NSPasteboard pasteboardWithName:NSDragPboard];
		NSEvent * currentEvent = [NSApp currentEvent];
		
		
		NSString * file = [cardView filePath];
		NSString * iden = cardView card] uniqueIdentifier];
		[[NSArray * fileList = [NSArray arrayWithObjects:file, nil];
		
		// Write data to the pasteboard.
		[pboard declareTypes:[NSArray arrayWithObjects:
			NSFilenamesPboardType,
			TRCardIdentifierPboardType,
			nil] owner:nil];
		[pboard setPropertyList:fileList forType:NSFilenamesPboardType];
		[pboard setString:iden forType:TRCardIdentifierPboardType];
		
		// Start the drag operation
		dragImage = [[NSWorkspace sharedWorkspace] iconForFile:file];
		dragPosition = [aTableView convertPoint:[currentEvent locationInWindow] fromView:nil];
		dragPosition.x -= 16;
		dragPosition.y += 16;
		
		[aTableView dragImage:dragImage 
						   at:dragPosition
					   offset:NSZeroSize
						event:currentEvent
				   pasteboard:pboard
					   source:self
					slideBack:YES];
					
The following works event wise, but I cannot drag to the desktop.
    		[uselessPBoard declareTypes:[NSArray arrayWithObjects:
			NSFilenamesPboardType,
			TRCardIdentifierPboardType,
			nil] owner:nil];
		[pboard declareTypes:[NSArray arrayWithObjects:
			NSFilenamesPboardType,
			TRCardIdentifierPboardType,
			nil] owner:nil];
		
		[uselessPBoard setPropertyList:fileList forType:NSFilenamesPboardType];
		[uselessPBoard setString:iden forType:TRCardIdentifierPboardType];
		[pboard setPropertyList:fileList forType:NSFilenamesPboardType];
		[pboard setString:iden forType:TRCardIdentifierPboardType];
		
		return YES;
		
Any ideas?

----
From Apple's docs:

*
A bug in NSTableView in Mac OS X version 10.2 and later causes cross-application drags to not work without additional code from the application developer. Drag-and-Drop within an application still works correctly.

You can work around the bug by subclassing NSTableView and overriding     draggingSourceOperationMaskForLocal: to return the appropriate     NSDragOperation (typically     NSDragOperationCopy, depending upon what drag operation you want the drag-and-drop to perform). Only applications built on Mac OS X version 10.2 and later are affected; applications built on Mac OS X version 10.1.x are not affected.

**Note:** This bug will be fixed in a future version of Mac OS X. Check this document at each release to determine if the bug has been fixed.
*

As of Panther, this still isn't fixed, but once you override the method, you can just put your data on the pasteboard by putting this code (below) in your data source. Hope this works! --JediKnil
    
- (BOOL)tableView:(NSTableView *)aTableView 
        writeRows:(NSArray *)rows 
	 toPasteboard:(NSPasteboard *)usefulPboard
{
	static NSArray *pboardTypes = nil; // This allows reuse of your types array

	if (pboardTypes == nil) {
		pboardTypes = [[NSArray alloc] initWithObjects:
			NSFilenamesPboardType,
			TRCardIdentifierPboardType,
			nil];
	}

	NSString *file = [cardView filePath];
	NSString *iden = cardView card] uniqueIdentifier];
	[[NSArray *fileList = [NSArray arrayWithObjects:file, nil];
		
	// Write data to the pasteboard.
	[usefulPboard declareTypes:pboardTypes owner:self];
	[usefulPboard setPropertyList:fileList forType:NSFilenamesPboardType];
	[usefulPboard setString:iden forType:TRCardIdentifierPboardType];
}


---- Many thanks!

