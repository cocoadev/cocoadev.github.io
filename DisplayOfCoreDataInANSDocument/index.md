---
layout: page
title: DisplayOfCoreDataInANSDocument
---

I have a NSDocument (not a NSPersistentDocument) where I load a data base file into for viewing (defined in a xcdatamodel).  I do define a NSManagedObjectContext variable (managedObjectContext) and a NSManagedObjectModel variable (managedObjectContext) with the appropriate Accessor methods.  In IB I bind the ArrayController (that controls the table that displays the data) two the managedObjectContext of the File's owner (the NSDocument).  The array is set to load the data automatically "automatically prepares content".

When I open the document, it loads the data file I indicate just fine (that is because the accessor method (NSManagedObjectContext *)managedObjectContext is called during the loading of the nib file and loads a dummy file [if managedObjectContext == nil)] ) - and the data is displayed.

But when I load a new data file (opened with a NSOpenPanel), the new data is loaded (its content is in the new managedObjectContext variable) but not displayed.  Any idea what I am doing wrong ?

Thanks a lot in advance.

     

- (NSManagedObjectContext *)managedObjectContext {
    
    NSError *error;
    NSPersistentStoreCoordinator *coordinator;
    if (managedObjectContext) { return managedObjectContext; }
	
	NSString *fileType = NSBinaryStoreType;
	NSURL *fileURL =  [NSURL fileURLWithPath:@"/Users/michael/dummy.binary"];
	coordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel: managedObjectModel];
	NSDictionary *storeOptionsDict = [NSDictionary dictionaryWithObject:[NSNumber numberWithBool:YES] forKey:NSReadOnlyPersistentStoreOption];
	if ([coordinator addPersistentStoreWithType:fileType configuration:nil URL:fileURL options:storeOptionsDict error:&error]){
		managedObjectContext = [[NSManagedObjectContext alloc] init];
		[managedObjectContext setPersistentStoreCoordinator: coordinator];
	}
	else [[NSApplication sharedApplication] presentError:error];
	[coordinator release];
	return managedObjectContext;
}

- (void) actionInOpenButton:(id)sender	{
	
	NSArray *fileTypes = [NSArray arrayWithObjects: @"xml", @"sql", @"binary",nil];
	NSOpenPanel *oPanel = [NSOpenPanel openPanel];
	[oPanel setAllowsMultipleSelection:NO];
	int result = [oPanel runModalForDirectory:nil file:nil types:fileTypes];
	
	if (result == NSOKButton) {
		NSPersistentStoreCoordinator *coordinator;
		NSError *error;
		NSString *fileType = oPanel filename]pathExtension];
		NSURL *fileURL =  [NSURL fileURLWithPath:[oPanel filename;
		if ([fileType isEqualToString:@"xml"])	{ fileType = NSXMLStoreType; }
		else if ([fileType isEqualToString:@"sql"])	 { fileType = NSSQLiteStoreType; }
		else if ([fileType isEqualToString:@"binary"]) { fileType = NSBinaryStoreType; }
		else {NSLog (@"Unrecognized file type \n"); }
		
		coordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel: managedObjectModel];
		NSDictionary *storeOptionsDict = [NSDictionary dictionaryWithObject:[NSNumber numberWithBool:YES] forKey:NSReadOnlyPersistentStoreOption];
		if ([coordinator addPersistentStoreWithType:fileType configuration:nil URL:fileURL options:storeOptionsDict error:&error]){
			managedObjectContext = [[NSManagedObjectContext alloc] init];
			[managedObjectContext setPersistentStoreCoordinator: coordinator];
		}
		else [[NSApplication sharedApplication] presentError:error];
		[coordinator release];
	}
	
}


----

Is there any particular reason why you're loading this in an NSDocument versus an NSPersistentDocument? There are a few discussions on the cocoa-dev mailing list that indicate this may be a bad idea due to a 'known issue' yet to be resolved.

----

mmmh I am not sure, here is why I did it: 

The NSDocument itself contains graphic objects (stored in a simple NSMutableArray) displayed in a NSView (part of the same NSDocument).  These objects have (almost) nothing to do with the data I am loading from the data file as the loaded data serves only as an aide to construct these graphic objects.  Since I am only interested in the graphic objects to be contained in the file, I opted for a NSDocument.

----

Ah - I remember you posted something like this elsewhere. I believe you were told then as well that this seems a very odd way of going about it. I do not believe CoreData was meant to be used in the way you're attempting above. I suggest you take the advice given to you there.

----

wasn't me :-) - you won't have any idea under what heading these suggestions were originally posted ?

----

(please use the reply tag. :-) ) ... Not off-hand. I will say, though, if you're storing everything together, it's unclear why you're taking this approach. If you want to use CoreData, then store your graphic objects as archived images (using the Binary Data type) inside your data model. It's becoming very apparent to me that you're massively over-complicating this task, which is why you're fighting CoreData. There's a lot of machinery you don't see (unless you look through the docs) in NSPersistentDocument that set up and handle CoreData's innards that NSDocument is completely ignorant of. This is a **bad approach**.

----

Ok - I guess I need to tell you more about my application.  There are in fact two different programs.

The first program (not yet mentioned) is a pure CoreData application that stores information about the BEHAVIOR of the graphic objects mentioned above.  Each object in this data base stores information about its possible states, actions it can perform, as well as cross-referenced with other objects in the data base it may interact with (these objects represent proteins).  However, no information on color, shape, coordinates etc. (drawing information) is stored.

The second application allows then to assemble (graphically) a system of interacting proteins by choosing protein-objects from the data base.  The information from the data base is only used to construct these instances (analogous to class-information), but have no representation in the system.  There are many more "templates" in the data base than represented in a given assemblage, and many of those will be represented in multiple copies.  I can give you an analogous example:

Let's say you have a data base of all cars ever manufactured. Each data base object contains information about a particular car type (performance, size etc.) but does not contain "instance" information (e.g. the color of a particular car, or how fast it is currently driving).  The second application (the traffic simulator), simulates traffic patterns in some city, in which the user chooses some of the cars from the data base (some types are chosen multiple times, others are not chosen) and place them on a NSView that represents the geography of that given city.  That is basically what I am doing.

So.. in my second application there is no need to represent these objects as NSManagedObjects and store them in a data base, as each "traffic simulation" will be composed out of different car assemblages.  However, I want the user to give a choice of the cars they can chose from to simulate their traffic pattern (stored in the data base).  In order to do so, I have a table next to my NSView that displays the �cars�.  Hence I have a NSDocument in which I want to open a CoreData document into.

----

