---
layout: page
title: CoreDataAcrossMultipleNibs
---

Ok well I've been working with Cocoa here for a while and am pretty well understanding how Core Data works, however one thing I am not getting to work is accessing the ManagedObjectContext from multiple nibs. For example my application has a main window where 99% of the activity will happen, but there is a need to setup some initial things for the user which I put into a separate nib ( StartupWizard.nib ) so that every-time the app loads it doesn't unnecessarily load resources it'll never use.

The problem is the app stops working (as in never saves data into the xml file) as soon as I instantiate the default AppDelegate class that contains the pointers to the NSManagedObjectContext, storecoordinator, etc in another nib. After thinking about this for a while I believe it could be allocating 2 instances of that class which are both trying to write to the same file which is why the xml file is created but contains no data. 

My question is what is the best way to access the same central ManagedObjectContext across multiple nibs? I have created and used some shared object instances before, but I don't know if that would work with Interface Builder or not? 

----

What you want to do is return the managedObjectContext from the app delegate in the window manager class like so

    
- (NSManagedObjectContext *) managedObjectContext {
	return [[NSApp delegate] managedObjectContext];
}


Then call it as normal from the managedObjectContext binding of your controllers

