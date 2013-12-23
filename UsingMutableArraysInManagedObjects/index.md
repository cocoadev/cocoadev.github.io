---
layout: page
title: UsingMutableArraysInManagedObjects
---



Upon execution of the following code (which produces no warnings):
    
// Create initial security group
General/NSManagedObject * group = General/[NSEntityDescription insertNewObjectForEntityForName:@"General/SecurityGroup" 
                                                    inManagedObjectContext:mainContext];
[group setValue:@"Private Assets" forKey:@"groupName"];
General/NSMutableArray * userArray = [group mutableArrayValueForKeyPath:@"users"];
 [userArray addObject:user];

... I get:
    
2005-05-04 13:45:17.122 General/MyApp[3670] General/NSManagedObjects of entity 'General/SecurityGroup' 
do not support -mutableArrayValueForKey: for the property 'users'


Why doesn't -mutableArrayValueForKey: work in this case?

----

To-many relationships are represented by General/NSMutableSet in Core Data. Try -mutableSetValueForKey:. --General/ScottStevenson

*Thanks! Works like a charm. I was completely reading that wrong, for what it's worth. I was reading it as 'set value for key, mutably'. :-} *

**Congrats! Retired.**

*Actually, can we keep this page linked to the General/CoreData topic? This is pretty useful information not everybody may grasp, with such a new technology.*

sounds good to me. people are way too quick to 'retire' pages  or otherwise orphan them. this site isn't a mailing list or a forum - it's a reference.

**To be fair, "retiring" a discussion merely means removing it from General/CocoaDiscussions, nothing more. Since that page is a list of open discussions, doesn't that make sense?**

----

Actuallty, this doesn't solve the problem at hand. It may have for the original poster, but there is a difference between a set and an array. Arrays maintain the order of their members, and sets do not. How can one use ARRAYS, or ordered sets in General/CoreData entities? General/CoreData doesn't currently provide this functionality out of the box.

I have skated by using an orderHint variable in the member object of unordered to-many relationships in General/CoreData, but this is hackish (though the preferred method according to an apple employee). I would prefer a cleaner solution. Any ideas?

-- General/EliotSimcoe

----
I think the idea behind using sets in General/CoreData is that most data *is* unordered, or is ordered by a primary key that is stored within the data itself. For example, a list of purchases is "naturally" ordered by date, but it could just as easily be ordered by price, or by store name, or whatever. The point is that a set can be sorted by any of these at any time, whereas an array has an intrinistic order. Also, sets do not support duplicates.

So, to get back to your question, an     orderHint variable probably *is* the best way to do this (and not really hackish at all), as long as the order does not depend on anything else. An example of *this* might be a playlist in iTunes, which can be sorted by many different fields, but can also be dragged into a specific order. --General/JediKnil 

----

Why would'nt you order the data with a General/NSFetchRequest, ordered with a relevant General/NSSortDescriptor ?

*Because this wouldn't preserve manual ordering as defined by the user - it'd sort by a descriptor, which may not be what the user (or developer) wants. General/JediKnil is correct - an orderHint (or whatever) variable is pretty much the only way to guarantee an order. You'd use the General/NSSortDescriptor to sort by that key. This allows your user to drag-n-drop the items in whatever order they want (which would trigger a re-enumeration of each item's orderHint keys) and still have that order preserved on subsequent fetches from your store. This is directly due to the fact that General/NSSets do not support orders. This has been discussed several times on the cocoa-dev mailing list (you might want to search www.cocoabuilder.com for the threads) and is the only currently-known approach to a solution for this problem.*
