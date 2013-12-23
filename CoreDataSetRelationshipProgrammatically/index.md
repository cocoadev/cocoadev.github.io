---
layout: page
title: CoreDataSetRelationshipProgrammatically
---



I have two NSArrayController objects and when I add a new object in each array, I would like to set the relationship between the two objects. In the OM, the relation type is already set but I don't know how to link the newly created objects together. How can I achieve this?

----

Ok, I managed to get this to work :

    
NSArray* tasks = [taskArrayController selectedObjects];
[attachmentArrayController setValue:[tasks objectAtIndex:0] forKeyPath:@"selection.task"];
NSArray* attachments = [attachmentArrayController selectedObjects];
[taskArrayController setValue:[attachments objectAtIndex:0] forKeyPath:@"selection.attachments"];



This is good for a one to one relationship, but what about a one to many relationship?

----

With Core Data, it's more conventional, at least, to work directly with NSManagedObject instances rather than make attribute changes through array controllers. Also, Core Data relationships are automatically two-way*, so you don't need to do the relationship in both directions. And therefore, you can most easily create a one-to-many link by setting the relationship attribute on the "many" object, which is automatically added to the list of related objects on the "one" side. 
If you're relating one "task" to many "attachments," something like this:
    
NSArray* tasks = [taskArrayController selectedObjects];
NSArray* attachments = [attachmentArrayController selectedObjects];
NSManagedObject *task = [tasks objectAtIndex:0];
NSManagedObject *attachment = [attachments objectAtIndex:0];
// link a two-way one-task to many-attachments relationship
[attachment setValue:task forKey:@"task"];

Alternatively (but harder), you could add a "many" object to the relationship on the "one" object via -mutableSetValueForKey: which returns a proxy for the set of related objects. You can call -addObject or -removeObject on that proxy. This is covered under "Using Managed Objects" in the Core Data Programming Guide. Read the Guide from start to end. Then read it again. Then wait a week and read it again. And you'll get a rough sense of things. To be clear, just set the relationship on one object or the other, not both. 

Core Data is not like most Cocoa programming--trying out code without understanding how it all works is pretty much doomed, because you can't trace through the operational code or set breakpoints in it. I've found that most "debugging" of Core Data consists of reviewing configurations (the model and the bindings) and re-reading the Guide :-). For the Guide, full-text search in Xcode Documentation window for "Core Data". 

*they are two-way by default, although one-way links are possible. There are also "delete policies" which allow you to do things like automatically delete all a task's attachments by just deleting the task.
--PaulCollins

