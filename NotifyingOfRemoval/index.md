---
layout: page
title: NotifyingOfRemoval
---

I have a container-esque object that contains an nsarray internally.  These containers depend on each other, as some are subsets of others, and so, when an object is removed it sends out a notification so that any others that are interested can do the same:

    
- (void)removeObject:(id)anObject
{
[array removeObject: anObject];

[[NSNotificationCenter defaultCenter]
 postNotificationName: StaticContainerDidRemoveObject
 object: self
 userInfo: [NSDictionary dictionaryWithObject: anObject forKey: @"Object"]];
}


The problem is, if none of the other containers have anObject, then the anObject I put in the dictionary has been released to 0, allowing for a potential crash or bug.  Is this an appropriate way to deal with this:

    
- (void)removeObject:(id)anObject
{
[array removeObject: [anObject retain]];

[[NSNotificationCenter defaultCenter]
 postNotificationName: StaticContainerDidRemoveObject
 object: self
 userInfo: [NSDictionary dictionaryWithObject: anObject forKey: @"Object"]];

[anObject release];
}


Thanks in advance,

- FranciscoTolmasky

*Good catch on the potential bug, and yes, that is a correct solution. You could also create the dictionary before removing the object which wouldn't require the two extra calls to anObject.*

