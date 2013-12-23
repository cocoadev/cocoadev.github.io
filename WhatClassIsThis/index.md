---
layout: page
title: WhatClassIsThis
---

Along the way to General/AspectCocoa I've encountered an interesting problem...

If I declare a method somewhere, and then use the runtime functions to lookup that method and add it to another class.  I have no way of knowing from within that method body exactly which class I am in.  I know which object I am in (self) but if I add a function to a class... and then I add it to it's superclass, I'll have no way of telling from within that methods implmentation which class it is in.  This becomes a problem if I want to lookup something specific to that class.  it's easy enough to get the class from self, and traverse the inheritance tree to find superclasses.  But If a method is added to a class and identically to it's superclass... there is no way to tell them apart.  This is most detrimental when an added method is required to call it superclass.  In which case we can have a method calling itself over and over again...

*    [self class] doesn't work as you'd hope? How odd...*

[self class] only returns the class of self... if for instance we are a method in the context of General/NSView but we're actually an object that is a subclass of General/NSView we won't be able to tell by [self class] if we are a method of General/NSView or a method of the subclass...

"    super" - Try using it.

Why are people treating me like an idiot?
super is resolved at compile time, it will always point to General/NSObject (or the superclass of wherever I originally declare my methods).. it is useless to me.  I need a dynamically defined super or self like thing..

----

Any luck resolving it with the     isa pointer?

I can't really see why you would want to do this.  I don't really see the point of adding the method to both classes.  Shouldn't you only add it to the superclass?  This just seems like there should be a way to solve the problem without doing any manual runtime hacking.  Perhaps there isn't (otherwise you wouldn't be here trying to do something the hard way) but it seems like there should be some way.

--General/JeffDisher
