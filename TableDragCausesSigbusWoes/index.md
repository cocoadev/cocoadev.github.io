---
layout: page
title: TableDragCausesSigbusWoes
---

It happens after the drop onto the table.

*On what line?*

Also, the "if ([supportedFiletypes containsObject:[filePath pathExtension]])"
statement does not seem to limit files types dropped as it should.

*What do supportedFiletypes and filePath contain at that point?*

----

Here's the code:

    

#import "ac3xController.h"

typedef enum {
    ac3xUnencoded,
    ac3xEncoding,
    ac3xEncoded
} ac3xEncodingState;

@interface Ac3xFile : General/NSObject
{
	@private
	
	General/NSString *filePath;
	ac3xEncodingState fileState;
}

+ (id) fileWithPath:(General/NSString *) aPath;

- (General/NSString *) filePath;
- (ac3xEncodingState) encodingState;

- (void) setEncoding;
- (void) setEncoded;

@end

@implementation Ac3xFile

- (id) initWithPath:(General/NSString *) aPath
{
	if ((self = [super init]) != nil)
	{
		filePath = [aPath copy];
		fileState = ac3xUnencoded;
	}
	
	return self;
}

- (void) dealloc
{
	[filePath release];
	
	[super dealloc];
}

+ (id) fileWithPath:(General/NSString *) aPath
{
	return General/[self alloc] initWithPath: aPath] autorelease];
}

- ([[NSString *) filePath
{
	return filePath;
}

- (ac3xEncodingState) encodingState
{
	return fileState;
}

- (void) setEncoding
{
	fileState = ac3xEncoding;
}

- (void) setEncoded
{
	fileState = ac3xEncoded;
}

@end

@implementation Ac3xController

// joar
//  You should *always* implement a dealloc method - you're leaking memory as it is now
- (void) dealloc
{
	[files release];
	
	[super dealloc];
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (void) awakeFromNib
{
	// joar
	// This can all be done in Interface Builder
	// id cell;
    // General/NSTableColumn *imageColumn = [sourceTable tableColumnWithIdentifier:@"image"];
    // cell = General/[NSImageCell new]; // Use of the "new" method is discouraged
    // [imageColumn setDataCell:cell];
	

    files = General/[[NSMutableArray alloc] init];
    
	[sourceTable setDoubleAction:@selector(encodeFile:)];
  
    [sourceTable registerForDraggedTypes:General/[NSArray arrayWithObjects:General/NSFilenamesPboardType,nil]];
   
	// joar
	// Should not the controller (ie. "self") be the receiver of the notification?
	// I have also commented it out until the callback methdod is implemented
    // General/[[NSNotificationCenter defaultCenter] addObserver: sourceTable selector: @selector(abortEncode:) name: General/NSApplicationWillTerminateNotification object:General/NSApp];
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (int)numberOfRowsInTableView:(General/NSTableView *)tableView
{
    return [files count];
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

+ (General/NSImage *) imageForState:(ac3xEncodingState) aState
{
	switch (aState)
	{
		case ac3xUnencoded :
			return General/[NSImage imageNamed: @"blue"];
		case ac3xEncoding :
			return General/[NSImage imageNamed: @"yellow"];
		case ac3xEncoded :
			return General/[NSImage imageNamed: @"green"];
		default :
			return nil;
	}
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

+ (General/NSString *) statusStringForState:(ac3xEncodingState) aState
{
	switch (aState)
	{
		case ac3xUnencoded :
			return @"";
		case ac3xEncoding :
			return @"Encoding...";
		case ac3xEncoded :
			return @"Encoded";
		default :
			return nil;
	}
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (id)tableView:(General/NSTableView *)view objectValueForTableColumn:(General/NSTableColumn *)col row:(int)row
{
	Ac3xFile *selectedFile = [files objectAtIndex: row];

    if (General/col identifier] isEqualTo: @"image"]) {
        return [[self class] imageForState: [selectedFile encodingState;
    }
    else if (General/col identifier] isEqualTo: @"source"]) {
        return [selectedFile filePath];
    }
    else if ([[col identifier] isEqualTo: @"status"]) {
        return [[self class] statusStringForState: [selectedFile encodingState;
    }
    return nil;


}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// joar
// Not used?
/*
- (General/NSString *)fileSelected
{ 
	// joar
	// Should you really have a "fileSelected" method, when you allow multiple selection in the table view?
	// You also need to handle the case when you have no selected rows and "selectedRow" returns -1!
return [files objectAtIndex: [sourceTable selectedRow]]; 

}
*/

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

/*
- (BOOL)tableView:(General/NSTableView *)tableView shouldEditTableColumn:(General/NSTableColumn *)tableColumn row:(int)row
{
	// joar
	// You can disable editing of column in IB - at least if you're always going to return NO.
    return NO; 
}
*/

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (General/NSDragOperation)tableView:(General/NSTableView*)tv validateDrop:(id <General/NSDraggingInfo>)info proposedRow:(int)row proposedDropOperation:(General/NSTableViewDropOperation)op
{
    [tv setDropRow:[tv numberOfRows] dropOperation:General/NSTableViewDropAbove];
    return General/NSTableViewDropAbove;
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (void) addFileAtPath:(General/NSString *) aPath
{
	[files addObject: [Ac3xFile fileWithPath: aPath]];
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (BOOL)tableView:(General/NSTableView*)tv acceptDrop:(id <General/NSDraggingInfo>)info row:(int)row dropOperation:(General/NSTableViewDropOperation)op
{

    General/NSPasteboard *myPasteboard=[info draggingPasteboard];
    General/NSArray *typeArray=General/[NSArray arrayWithObjects:General/NSFilenamesPboardType,nil];
	General/NSArray *filenames = [myPasteboard propertyListForType:General/NSFilenamesPboardType];
    General/NSArray *supportedFiletypes = General/[NSArray arrayWithObjects:@"dv", @"VOB", nil];

    General/NSString *filePath,*availableType;
    General/NSArray *filesList;
    int i;

    availableType=[myPasteboard availableTypeFromArray:typeArray];
    filesList=[myPasteboard propertyListForType:availableType];
	
	// joar
	// You're assigning an autoreleased object instance to an instance variable - not good
	// In the general case this would have led to a crash
	// statusImage = General/[NSImage imageNamed: @"blue"];
	// Same here really. OK, so a static string doesn't have to be retained, but it's best to
	// learn to use proper memory management routines at all times
	// statusText = @"";

    for (i = 0; i < [filesList count]; i++)
    {
		if ([supportedFiletypes containsObject: [filePath pathExtension]])
		{
			[self addFileAtPath: filePath];
		}
	}
		
		[sourceTable reloadData];

		/*
        filePath=General/filesList objectAtIndex:i]lastPathComponent];
		
		if ([supportedFiletypes containsObject:[filePath pathExtension)
{
        [files insertObject:filePath atIndex:row+i];
    }
   
    [sourceTable reloadData];
   
    [sourceTable selectRow:row+i-1 byExtendingSelection:NO];
		 */
		
    return YES;
   // }
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (General/IBAction)abort:(id)sender
{
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (General/IBAction)addFile:(id)sender
{

  int result;
  General/NSArray* fileTypes = General/[NSArray arrayWithObjects: @"dv", @"VOB", nil];
  General/NSOpenPanel *oPanel = General/[NSOpenPanel openPanel];

  [oPanel setAllowsMultipleSelection:YES];
  result = [oPanel runModalForDirectory:General/NSHomeDirectory() file:nil types:fileTypes];
  
    if (result == General/NSOKButton)
	{
        BOOL x;
        General/NSArray *filesToOpen = [oPanel filenames];
        int i, count = [filesToOpen count];
        for (i=0; i<count; i++)
		{
			[self addFileAtPath: [filesToOpen objectAtIndex: i]];
/*        
            General/NSString *aFile = General/filesToOpen objectAtIndex: i] lastPathComponent];
            [files insertObject:aFile atIndex:i];
			*/
        }
        [sourceTable reloadData];
		[sourceTable selectRow:[files count]-1 byExtendingSelection:NO];
    }

}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- ([[IBAction)deleteFile:(id)sender
{

 General/NSEnumerator *enumerator = [sourceTable selectedRowEnumerator];
    id object;
    int i = 0;
    while (object = [enumerator nextObject]) 
    {	
        General/NSLog(@"%@",object);
        [files removeObjectAtIndex:[object intValue]-i];
        i++;
    }
    
    [sourceTable reloadData];
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////

- (General/IBAction) encode:(id) sender
{
	int selectedRow = [sourceTable selectedRow];
	
	if (selectedRow >= 0)
	{
		Ac3xFile *selectedFile = [files objectAtIndex: selectedRow];

		[selectedFile setEncoding];
	}

	/*
    {	
		// joar
		// You're assigning an autoreleased object instance to an instance variable - not good
		// In the general case this would have led to a crash
		// statusImage = General/[NSImage imageNamed: @"green"];
		// Same here really. OK, so a static string doesn't have to be retained, but it's best to
		// learn to use proper memory management routines at all times
		// statusText = @"Encoding...";
    }
	 */
    
    [sourceTable reloadData];

}

@end



Thanks for your help!

Sean


----

You gotta be more specific. When exactly does the problem occur? While you're dragging? When you start the drag? When you end the drag?

*Editor's Note: thanks to joar for trying to debug this code; where advice is given, comments have been inserted in the code*
