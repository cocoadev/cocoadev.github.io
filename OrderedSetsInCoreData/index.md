---
layout: page
title: OrderedSetsInCoreData
---



Good morning everyone,

I'm writing a CoreData app and am struggling to model an ordered to-many relationship. I can't find any docs detailing an appropraite solution. Any hints?

Thanks.

EliotSimcoe

----

You should just place an 'orderInList' numerical property into the entity that will appear in the to-many relationship. As they are d-n-d'd or otherwise placed into their order, simply assign each entity's preferred index to its orderInList value. For instance, if you have a Foo object with a to-many relationship to a Bar object, Bar should contain a property called orderInList. When Foo's Bar objects are somehow rearranged, trigger the index assignment.

----

So there is no support for ordered to-many relationships in the controller layer?

----

I haven't personally read across anything in the docs myself.

----

**No, it's definite - there is no support for ordered anything in CoreData. The answer above is quite correct and complete. I have this working in my own app and it was fairly simple and straightforward.**

----
Or adding a next and previous property. That might perform better.

*Known as a 'linked list' ... maybe someone can fill in a page for CoreDataLinkedLists or something similar.*

----
Wouldn't a linked list require a lot of joins being fired to the underlying database?

*Not sure what you mean, but re-indexing other instances because an instance near the 'top' of the list is moved would cause all of those instances to be faulted in (at least for a little while). There's really no way to avoid this, unless you create a separate entity for tracking index order. That same methodology could be applied to linked lists. In other words, create an entity called LinkedListJoin with relationships like "leftObject" and "rightObject". If the order of the linked objects are changed, all that gets faulted in is the LinkedListJoin objects. Now sorting by this would become a little more complicated .... :-)*

----
The advantage of a linked list is the constant time for insertion and removal, but getting the actual sorted array takes a lot of time because the faults for all the to-one relationships describing the linked list have to be fired. Batch-faulting could be a solution for this, but it is not yet reliable in CoreData.
A trick is to define your 'orderInList' as a floating point number. So you can easily provide constant insertion and removal.

*Would you mind elaborating on this? This seems like a good idea.*

Sure. This is for sorting a to-many relationship. 

* Add a floating point attribute named 'sortOrder' to the target entity of your to-many relationship.
* In the custom class of the source entity implement a method thats returns an array with the content of the relationship sorted by 'sortOrder'
* To insert an object between two existing ones named "previous" and "next", set its 'sortOrder' to (previous.sortOrder+next.sortOrder)/2.0f
* To delete an object, just delete it.
* If when inserting, the delta of the sortOrders falls below a certain very small threshold, renumber your whole relationship.


The disadvantages are:

* You need to have the whole relationship fetched in memory, but in most cases you have it anyway.
* The renumbering process scales with O(n) but might occur very seldom, depending on the insertion behaviour.


I know, it's quite a hack, but it works.

