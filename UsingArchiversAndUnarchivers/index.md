---
layout: page
title: UsingArchiversAndUnarchivers
---

If you have an General/NSDocument derived class and you want to save out a single object from your class, you can use the code below in your     dataRepresentationOfType: method.

    
General/[NSArchiver archivedDataWithRootObject: singleObject];


Put the code below in the corresponding     loadDataRepresentation:ofType: method.

    
[singleObject autoRelease];
singleObject = General/[[NSUnarchiver unarchiveObjectWithData: data] retain];


Anyone care to add code to add multiple objects to the data stream? Shouldn't be too difficult...

----
Ok,

If you want to have your document archive a collection of objects then just have an instance (in your Document Class) that acts as the model (or root object) for the collection of objects you want to archive.  This can be one of the Foundation collection Classes, (General/NSArray, General/NSDictionary, General/NSSet) or a custom Class of your own that implements the General/NSCoding protocol.

Now your document's archivable data is encapsulated in this model object.  If you want to read or change data within the model (i.e. data that will be saved) then you have to use the model's accessor methods 
    
e.g. [someIBOutlet setColour: [documentModel theColour]];



When you come to archive in the     dataRepresentationOfType: method you simply archive the model object:

    
- (General/NSData *)dataRepresentationOfType:(General/NSString *)aType
{
    // (Saving)  The General/NSDocumentController wants to save the document to disc.
    //	We need to give it our model object as General/NSData.
    General/NSLog(@"Document dataRepresentationOfType");

    return General/[NSArchiver archivedDataWithRootObject:documentModel];
}



and the same goes for unArchiving:

    
- (BOOL)loadDataRepresentation:(General/NSData *)data ofType:(General/NSString *)aType
{
     [documentModel release];
    documentModel=General/[[NSUnarchiver unarchiveObjectWithData:data] retain];

    [self updateUI];
    return YES;
}




----

Also, anyone know how to add versioning to the archiver stream so the file format can evolve nicely?

Here's version info: General/VersioningUsingCoder -- General/ChrisMeyer

----

Here's a link to a discussion about portability and plans for General/NSArchiver/General/NSUnarchiver:
http://mail.gnu.org/pipermail/help-gnustep/2000-September/000001.html

----

Could some1 contribute some examples of the use of those?

General/ArchivingToAndUnarchivingFromPropertyLists General/ArchivingObjects

----
hmm,
assume I have a class Person, which implements a <General/NSCoding> protocol. The data are used in a tableview and I want to use drag and drop. The problem is in case of multiple rows are selected/dragged. I can archive all data into General/NSArchiver object easy but how can I Unarchive them? 

    
General/[NSUnarchiver unarchiveObjectWithData:[pboard dataForType:General/SomeDragDropPboardType]]


this function will discard any data behind first record. How to correctly do it?
Thanks -General/BobC

----
ok,
I'm posting here a right solution.

    
    General/NSUnarchiver *unarch = General/[[NSUnarchiver alloc] initForReadingWithData:[pboard dataForType: General/SomeDragDropPboardType]];
    
    while (![unarch isAtEnd])
    {
        [_array addObject:[unarch decodeObject]];
        General/NSLog(@"%d", [unarch isAtEnd]);
    } 

    [unarch release];


However, I'm getting an error message "*** Incorrect archive: unexpected byte". Any clues?
-General/BobC

----
The problem is that General/NSUnarchiver doesn't know how to expand multiple objects. The easiest solution I have found so far is to inlcude all objects into one array, which can be archived easily and than unarchive it on another side. I'm posting the solution:

*Perhaps you forgot to look at the superclass?  To decode multiple objects with General/NSUnarchiver, init it with data and call decodeObject repeatedly.*

dragsource:
    
- (BOOL)tableView:(General/NSTableView *)tv writeRows:(General/NSArray*)rows toPasteboard:(General/NSPasteboard*)pboard
{
    General/NSEnumerator 	*enumerator = [rows objectEnumerator];
    General/NSNumber 		*currentRow = General/[NSNumber numberWithInt:0];
    General/NSMutableArray	*draggedObjects = General/[NSMutableArray arrayWithCapacity:1];
    General/NSData		*aData;

    //prepare data
    while (currentRow = [enumerator nextObject])
    {
	General/ICISPerson *aPerson = General/currentData peopleArray] objectAtIndex:[currentRow intValue;
	[draggedObjects addObject:aPerson];
    }
    aData = General/[NSArchiver archivedDataWithRootObject:draggedObjects];
        
    [pboard declareTypes:General/[NSArray arrayWithObjects: General/ICISDragDropSimplePboardType,General/NSStringPboardType, nil] owner:self];
    
    [pboard setData:aData forType:General/ICISDragDropSimplePboardType]; 
    [pboard setString: [aData description] forType: General/NSStringPboardType];

    return YES;
}

drag destination
    
- (BOOL)tableView:(General/NSTableView*)tableView acceptDrop:(id <General/NSDraggingInfo>)info row:(int)row dropOperation:(General/NSTableViewDropOperation)operation
{
	[preferredPeople addObjectsFromArray:General/[NSUnarchiver unarchiveObjectWithData:General/info draggingPasteboard] dataForType:[[ICISDragDropSimplePboardType]]];
	[tableView reloadData];

    return YES;
}

the drag destination solution is by Marcel Weiher
- General/BobC

----

Please note that General/NSKeyedArchiver and General/NSKeyedUnarchiver are usually to be used in preference to the unkeyed version.  Key-value coding/archiving is a lot more portable across versions, since it doesn't depend on objects to have been archived in a particular order.  It's my understanding that unkeyed archiving is more or less deprecated, but I might just be projecting there.   ---  General/JoeOsborn
