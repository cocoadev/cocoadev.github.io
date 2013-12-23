---
layout: page
title: CoreDataAndNSOutlineView
---




Hi, all. I have an application that has several different kinds of entities. They represent different objects in a project. I have a list of project members that I want to display in an outline view. I don't want to support *manual drag order*, but I *do* want to be able to accept things like images, text files, etc. To do this, I imagine that, on accept/drop, I'd create the appropriate entity ("Illustration" for image drops, etc.) and the outline view would do the right thing.

My question is twofold: 

- First, is the above approach to drag-n-drop "The Right Approach"? In other words, rather than support dragging the items to any particular row (black line between insertion rows), just accept the drop to the outline view itself (black rectangle around entire outline view).

- Second, how would I represent this structure with the outline view itself (Bindings-wise)? More to the point, what do I need to know to create a tree where several different entities with some common attributes and some unique attributes are represented, separated by their type? For example, "Illustration" instances would appear under a main "Illustrations" node, etc. Is it even *possible* to have entities with varying attributes represented in an outline view?

  Lastly, if this information has been posted elsewhere, I apologize; could you point me to it with perhaps a simple line explaining its relevance/importance to my goal? This would help future searchers find the information more easily. Thanks for *any* help!

----

**October 11 2005**

I've given up trying to represent this all in one outline view, since each type will have its own properties represented in differing columns. I've since created a view that contains an outline view for each of the major object types I'll be representing, all in one scroll view. Any time any of their bounds change, I tile them with my own custom routine. It works quite nicely. Now my objective has changed. Concentrating only on one entity:

I have an entity called "Entry" and one called "Folder". The relationship is:     Folder <-->> Entry. In ONE outline view I'd like to represent all the folders and entries. The problem I'm trying to wrap my head around is this: I want an entry to appear as a root object if it has no parent folder, so the outline view must represent folders and entries. I thought about making Folder and Entry both have a common abstract parent like "Container" but I don't want Entry instances to contain anything (ie, an entry cannot contain an entry or a folder). Just Folder instances. :-/ So how do I represent this?

----

*Just like a filesystem. Make a root folder that contains **everything**, then display its contents. Any root object has this special root folder as its parent. The user doesn't need to know about the root folder at all if you only show what's in it. --JediKnil*

