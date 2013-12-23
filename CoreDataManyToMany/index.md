---
layout: page
title: CoreDataManyToMany
---

Is this is a bug in CoreData or is its just something to be aware of.

I have entities called Person and SpouseRel. A Person may have many spousal relationships (SpouseRels).
A SpouseRel must have 2 partners (may be the same sex). 
A SpouseRel may also be related to <0:M> Events (marriage, separation, divorce, etc). 
I thus have a many-to-many relationship between Person and SpouseRel and CoreData handles that very nicely too. 

When I add spouse to a person I insert a new SpouseRel into my ManagedObjectContext and then add personA and personB to the set of the SpouseRel's partners using: 

spouseRel mutableSetValueForKey:@"partners"] addObjectsFromArray:[[[NSArray arrayWithObjects:personA, personB, nil]];

But later on ** within the same event loop ** I ask for the reverse of the relationship using: 

[person mutableSetValueForKey:@"spouseRels"] 
and I get an empty set returned.  

So is it true that for a many-to-many relationship I cannot assume that the reverse relationships exist until the end of the current event loop?
Where in the documenatation does it explain this?  (Have I missed it again?) 

Regards, Keith from DownUnder, 24 Nov 05
----

Try sending the     processPendingChanges message to your NSManagedObjectContext. It's in the documentation somewhere...I can't find it right now.

