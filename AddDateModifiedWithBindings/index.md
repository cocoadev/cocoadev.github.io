---
layout: page
title: AddDateModifiedWithBindings
---

Hi,

I have a question regarding bindings, key-value-observing, and updating the persisting document.

Let's say have an entity called "Employee" with attributes "name", "dateCreated", and "dateModified".  After initially creating the object, I would like to update the attribute "dateModified" whenever I change something in my object.  Now, everything is done through bindings and it seems I can't get to permanently update "dateModified".

I can get the "dateModified" updated on my screen (through key-value-observing) using "triggerChangeNotificationsForDependentKey" in "initialize":
     
 +(void) initialize	{
 	if (self == [Employee class])	{
 		NSArray *keys = [NSArray arrayWithObjects:
 			@"name", nil];
 		[self setKeys:keys triggerChangeNotificationsForDependentKey:@"dateModified"];
 		
 	}
 }

but I can't figure out how to make this change permanent when I save the document (I never use setValue:newDate forKey:@"dateModified" so obviously my changes are not permanent, but I don't know how to trigger this method and where to put this line in my code).

Do I have to go through all the trouble of implementing "addObserver" in the document object to observe changes to the "Employee" objects or is there an elegant way to do it through bindings or only a few lines of code.

Thanks a lot, Michael
----
Well, for one thing, the     addObserver:... methods are already implemented in all objects by default (see the Foundation NSKeyValueObserving protocol), so you can easily add an observer if you wish. However, there are two points you need to remember. First, any key-value compliant method (    -value and     -setValue) will notify observers, so there is not always a need to use     setValue:forKey:. In your case, though, the easiest thing to do would be to set the date modified in all of your other methods (in addition to your current     initialize code, which should probably be inheritable -- no     self == [Employee class]), like so: --JediKnil

    
 - (void)setName:(NSString *)newName
 {
 	if (newName != name) {
 		[name release];
 		name = [newName retain];
 		[self setDateModified:[NSDate date]];
 	}
 }

----
Thank you so much for the swift and clever answer.  What I did not state clearly in my initial post is that the "Employee" class is entirely coded with xcdatamodel (as Entity).  Hence, the above solution works only if you code your object programmatically.  I posted the code for working with Entities below (in case someone runs into the same problem) - which is exactly the same solution as JediKnil proposed.


    
 - (void)setName:(NSString *)newName	{
 	
 	[self willChangeValueForKey:@"name"];
 	
 	if ([self valueForKey:@"name"] != newName)	{
 		[self willChangeValueForKey:@"dateModified"];
 		[self setPrimitiveValue:newName forKey:@"name"];
 		[self setPrimitiveValue:[NSDate date] forKey:@"dateModified"];
 		[self didChangeValueForKey:@"name"];
 		[self didChangeValueForKey:@"dateModified"];
 	}
 	else	{
 		[self didAccessValueForKey:@"name"];
 	}
 }

