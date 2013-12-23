---
layout: page
title: MemoryManagementRegardingSubItems
---

Let's say you have a parent class, and your parent class has children within it. For each child, you wanted it to store a reference to its parent. So you create accessor methods     setParent: and     parent. Now here's the question. Do you retain the parent in each child?
    
- (void)setParent:(id)parent
{
  if (_parent != parent)
  {
    [_parent release];
    _parent = [parent retain];
  }
}
Or do you just do this:
    
- (void)setParent:(id)parent
{
  _parent = parent;
}
? ? ?

----

Good question - this is a weakness of the ObjC ref counting scheme.  Use your judgment.  In many cases, if the children retain the parent it will create a retain loop, which will have to be broken at some point if you expect the objects to ever be deallocated.  So it might depend on the complexity of the situation;  it's easiest to have the children not retain the parent, but if that's going to make you introduce bugs then you'll have to be more sophisticated.

Note that cocoa view objects don't retain their delegates or datasources.

See http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Concepts/ObjectOwnership.html under "Weak References" for Apple's take on it.

The WeakReferences and WeakPointers pages deal with fairly sophisticated, general workarounds.

----
I was just about to post this when I remembered the dangers of MultipleEditSyndrome...anyway, here's my post, even though it is now slightly redundant:

This situation is very similar to delegation. If you create a class M<nowiki/>yWindowController, and set it to be the delegate of an NSWindow, the NSWindow does not retain the instance of M<nowiki/>yWindowController. Why? If the delegate is released everywhere else, the window should not keep it alive. This also prevents circular references, as usually the delegate class will retain a reference to the window. In your case, the parent class (which likely survives longer) should retain references to its children (probably in an NSArray or NSDictionary), whereas the children should not retain the references to the parent. --JediKnil

----

Normally you should do something like this:

    
- (void)removeSubItem:item {
    [item setParent:nil]; // this is important
    [subitems removeObject:item];
}

- (void)setParent:newParent {
    parent = newParent;
}


The line commented as "this is important" ensures that children never have stale references to their parents; when they're removed from a parent, their reference is set to nil.

