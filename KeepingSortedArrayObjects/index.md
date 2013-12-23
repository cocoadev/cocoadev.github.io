---
layout: page
title: KeepingSortedArrayObjects
---

Okay, this may be a stupid question, but I've noticed that after I started using CoreData, it doesn't keep the items in an entity/array sorted through each opening of a file (in a doc-based app). When I open the file multiple times, the objects are sorted differently every time. Is there some way to send the sorting info with the file when it's saved, or is it just some easy method to implement? --LoganCollins

----
**'sigh* OrderedSetsInCoreData UsingMutableArraysInManagedObjects CoreDataQuestions*
----

I read over those pages before I asked, but wasn't sure if it was applicable to my situation. I have a table of items that can be dragged and dropped into whatever order the user needs, and I just wanted it to save that order when the file is saved. Is the only way to actually put an index or similar attribute into the entity?

