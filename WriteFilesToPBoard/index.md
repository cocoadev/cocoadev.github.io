---
layout: page
title: WriteFilesToPBoard
---

Each of the items in my outlineview has a path property which is a valid path pointing to it's location on the system.  You can only drag one item at a time, and I am using the code below.  I can drag it fine, but other applications do not accept it as a drop (for instance I cannot drop it into the dock or in a folder in the finder).  Am I missing something?

    
- (BOOL)outlineView:(General/NSOutlineView*)olv writeItems:(General/NSArray*)items
	   toPasteboard:(General/NSPasteboard*)pboard
{
	General/NSArray *types = General/[NSArray arrayWithObjects:General/NSFilenamesPboardType, nil];
    [pboard declareTypes:types owner:self];
	General/NSString * path = General/items objectAtIndex:0]keyValue:@"path"];
	[[NSArray *fileList = General/[NSArray arrayWithObjects:path, nil];
    [pboard setPropertyList:fileList forType:General/NSFilenamesPboardType];
 
    return YES;
}

