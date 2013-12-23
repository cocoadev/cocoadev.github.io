---
layout: page
title: NSDocumentControllerProblem
---

Im trying to figure out what may be going here, basically I am working on a Core Data Document based app. When I begin to load a view in an external nib into the app I create and initialize a NSNib instance then load the view as the content view in a NSBox on the window belonging to MyDocument. The View loads fine but if I try to create a NSManagedObjectContext method in the controller class of the view that was just loaded using the following code

    
- (NSManagedObjectContext *)managedObjectContext
{
	return [[[NSDocumentController sharedDocumentController] currentDocument] managedObjectContext];
}


it spits out the error "*** NSRunLoop ignoring exception 'Cannot perform operation without a managed object context' that raised during posting of delayed perform with target 3bf160 and selector 'invokeWithTarget"

but if I do 

- (NSManagedObjectContext *)managedObjectContext
{
	return [[[[NSDocumentController sharedDocumentController] documents] objectAtIndex:0] managedObjectContext];
}

it works fine. Can anybody help me understand why the current document method fails but the object at index method works fine? The object at index method is just asking for trouble later on.

----
    -currentDocument is documented as returning nil in various circumstances, so it's probably not the best idea to use it.

