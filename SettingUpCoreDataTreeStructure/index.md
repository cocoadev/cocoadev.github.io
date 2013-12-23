---
layout: page
title: SettingUpCoreDataTreeStructure
---

I've tried wrapping my head around this for over a week now. Basically, I want to create a tree data structure using CoreData, and I want to display it in an NSOutlineView. I have two entities, "Item" and "Folder" and I can't figure out what relationships I need to create between these. I want folders to be able to contain items and other folders (recursively), but items must not contain other stuff. Also, I can't figure out how to display both my folders and items in one single NSOutlineView. Someone hinted that I might need an extra "RootFolder" parent entity to get the tree structure right...?

----
One common way to encode a tree is to have a parent pointer with every item. This approach works reasonably well in a database since you never need more than one per item. For listing a folder, you would query for all objects that have that folder set as its parent. --Joakim
----

Yes, but how would I model this in the CoreData entity editor?

----
Assuming you have a "Folder" entity and a "Item" entity, both "folder" and "item" would have a "folder" to-one relationship. The "Item" entity's -folder relationship would point to the "Folder" entity. The "Folder" entity's -folder relationship would point to itself (the "Folder" entity). In this case, it's easier not to have a "children" relationship since you have two different entities here, one that has no children (the "Item" entity). 

An alternative would be to create one "Container" type of entity, an abstract. It has both "parent" to-one relationship to itself and a "children" to-many relationship also to itself. "Folder" and "Item" then set their parent entity to "Container" to inherit this behavior. You'd need an "Item" subclass to always prevent the addition of "children" and always return an empty set when asked.
----

Thanks a lot! I'll give it another try.

