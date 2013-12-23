---
layout: page
title: CoreDataBasicObjectManipulation
---

Good day all, I am currently trying to write a CoreData back end for one of my programs. However straight to the point I am working from the default template modified. I am attempting to get it to work reliably. Currently with the following code and the object model ( http://catsdorule.torpedobird.com/tmp/DataModel.pdf ):

    - init
{
	if (fm == nil) fm = [NSFileManager defaultManager];
	if (fm == nil) ws = [NSWorkspace sharedWorkspace];
	if (pr == nil) pr = [NSUserDefaults standardUserDefaults];
	if (nc == nil) nc = [NSNotificationCenter defaultCenter];
	if (wnc == nil) wnc = [ws notificationCenter];
	if ((self = [super init]) != nil)
	{
		NSManagedObjectContext * context = [self objectContext];
		TRLog(@"%@",context);
		
		NSManagedObject * card = [NSEntityDescription insertNewObjectForEntityForName:@"Card" inManagedObjectContext:context];
		NSManagedObject * descEntry = [NSEntityDescription insertNewObjectForEntityForName:@"DescriptionEntry" inManagedObjectContext:context];
		[card setValue:descEntry forKey:@"descriptionEntry"];
		[descEntry setValue:card forKey:@"card"];
		
		NSSet * insertedObjects = [context insertedObjects];
		TRLog(@"%@",insertedObjects);
	}
	return self;
}

Currently if we only have the line "NSManagedObject * card = [NSEntityDescription insertNewObjectForEntityForName:@"Card" inManagedObjectContext:context];" it works and every time the application is started another "card" is added to the set. However If I try to create any sub objects it seems to work however when closed I get an error like "Multiple Validation Errors Occurred".

Perhaps the eyes of others experienced in core data could assist me with this problem.

----

Completely off the subject of your question, but I think it bears mentioning; you need to retain your     fm,     ws, and other variables, since you didn't get them from a method called     alloc,     copy, or     retain. It's probably safe since they're singletons, but that's relying on an implementation detail and you're setting yourself up for a crash later on.

Sorry, but I can't answer your question, I don't know enough about CoreData.

----

Check what attributes are marked as required rather than optional. Also check whether the relationships allow zero...

----

I'm getting this "Multiple validation errors occurred" error for my application as well.  Is there some debugging to turn on to see what constraints are being violated. I've checked my model and it appears I've satisfied all the constraints..  :-/

----

See if the template you used has an application delegate with an applicationShouldTerminate: implementation. You should find some error handling code in there, where you can set a breakpoint or insert something to display more detail. Apple's NSPersistentDocument Core Data Tutorial has an example of getting the error detail when multiple errors occur.

----

Another thing that can cause this is having a hanging object. e.g. if class A has a collection of B, and you clear that collection without actually explicitely deleting the B objects, you can get Multiple validation errors.

