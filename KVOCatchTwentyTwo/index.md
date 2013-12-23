---
layout: page
title: KVOCatchTwentyTwo
---



Seems there's something fundamentally flawed with Cocoa's KVO implementation. Or more likely, I'm just not getting something. My situation is somewhat complex, but it boils down to this:

I have two objects, A and B, and A wants to observe changes in B. The reality of the situation is that in fact B is one of many possible leaf nodes of a tree, and A could be its root, though it doesn't have to be - A could be another object altogether. Anyway the point is that B has many properties which should be undoable, so the purpose of A is to receive changes notifications via KVO and forward them as an invocation to the undo manager, and to receive the undo manager's invocation and call the original object back using KVC. This is all fine, and pretty much as given in the example in Hillegass, on page 148.

Now, the rules of KVO state that an object must be removed as an observer before the observed can be deallocated - if you don't you get an exception from the overridden -dealloc method of the swizzled observed object class. In addition, it's a general rule that observers shouldn't be retained.

So here's the catch-22. My observer and by observed objects can be deleted (dealloced) at any time, and I need to make sure this works safely. However, it would be nice if the observed object didn't have to know anything about who is observing it. But it apparently has to, because otherwise it cannot tell the observer to remove itself when it is deleted. Furthermore, the exception is thrown before the observed object's -dealloc method gets called, meaning that you have to resort to a nasty hack like overriding release instead and disconnecting the observer if the dealloc will be called, i.e. when the retain count is 1. This can't be right. Also, because the observed object can't retain the observer, if the observer gets dealloced first, unless it takes care to remove itself from anyone it's ever started observing, the reference in the observed is stale, so that crashes. The observed also might have multiple observers, so it needs to keep track of all of them, so the problem gets very messy. This could all be very neatly solved by Cocoa itself just disconnecting observers at dealloc time instead of throwing an exception (who the hell thought this was a good idea? It already has the info needed to do it in its private stuff) but it doesn't, so a solution needs to be found.

Hillegass handles it the other way around, where the observer is responsible for tracking and deleting all the observed objects, and that might be the way forward. But it's a very poor fit with my actual design, where each leaf node is fairly self-contained and only needs to know about its immediate parent to be able to be added to the tree. By forcing the observer (tree root or some other object) to handle all this stuff as well makes it really awkward and complicated instead of simple and elegant.

I might also mention that I am not using Core Data (to do so would require a complete rewrite of more than 150 classes).

Has anyone already hit this snag? If so, how did you solve it? And why is KVO so picky and fragile instead of being helpful and robust, like most other stuff? --GrahamCox

----
Because the goal is to support undo, it might be better to take an approach I've seen before: a     deleted flag. Rather than deallocating the node during its lifetime, just set its deleted flag and hide it. Actually deallocate it when the document is closed (or, if you need to save RAM, when the user saves), either by maintaining a set of deleted objects or by walking the tree.

And I would guess KVO is "picky and fragile" to make it as lightweight as possible, though compared to KVC it's not nearly as easy or as elegant. --JediKnil
----

For a tree, you generally want to stop observing changes to a leaf when it is removed from the tree. If you observe the addLeaf and removeLeaf operations on the leaf's parent, it is very easy for you to stop observing the leaf. -- DavidPhillipOster

----
Another way (a bit hackish though) might be to include a toBeDeleted flag which is also observed. The delete method starts off by setting this, triggering a change observation which allows all observeres to disconnect themselves. The delete method can then continue with the delete. -- JulianBlow

