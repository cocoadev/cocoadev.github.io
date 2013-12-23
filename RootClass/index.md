---
layout: page
title: RootClass
---

A RootClass is a class which has no superclass. In many languages, such as Java, people speak of **the** root class.  This is because every class is expected to descend from a unique root object (java.lang.Object in java).

ObjC is not such a language.  In ObjC the inheritance graph is a disjoint union of trees, which is called a forest in graph theory.  We call the root of each tree a RootClass.  

Cocoa/ObjC exposes 3 public root classes, and there is nothing to stop the user from adding more (though the documentation warns that this is tricky).  Cocoa itself defines an additional 5 (!) private root classes.  I'll list them here for interest's sake.

**Public root classes in Cocoa/ObjC:**

* NSObject is main root class in the framework.  Nearly all cocoa classes descend from it.

* NSProxy is the main root class for objects that stand in for other objects.  See NSProxy for details.

NSProtocolChecker and NSDistantObject are public cocoa classes that descend from NSProxy.

* The last public root class in the framework is     Object, defined in file:///usr/include/objc/Object.h.  This is a legacy class - it was the main root class in old versions of the NeXT operating system.  Our only contact with it these days is that it is the root class of the Protocol class, whose objects are FormalProtocols.

Object does not implement the NSObject protocol (it's a protocol as well as a class - check the docs), so instances of this class are not first-class objects in cocoa.  In particular, Object does not implement     retain or     release.  This does have some impact on us - you cannot directly put Protocol objects into an NSArray or NSDictionary!



And continuing..



**Private root classes in Cocoa/ObjC:**

None of the following classes have any subclasses in cocoa.

*  _NSZombie - This is a root class used to implement the very cool debugging feature described at DebuggingAutorelease. 
* NSInvocationBuilder - Hey, cool, I've implemented this before!  Not sure why it needs to be a root class though.

This is a convenience class for constructing invocations.  It exploits message forwarding to allow the following:
    
NSInvocation *crystallizedMessage = 
    [[NSInvocationBuilder builderForObject:tableDataSource] tableView:tableView 
                                            objectValueForTableColumn:tableColumn 
                                                                  row:4]; 


* NSDeallocatedObject and NSResurrectedObject - classes in support of ObjC's new (as of 10.4) GarbageCollection.  These are part
of Darwin, so see objc-auto.m in http://www.opensource.apple.com/darwinsource/tarballs/apsl/objc4-266.tar.gz .
* NSLeafProxy - I don't know what this is for.  It has something to do with the filesystem.  A google search for "leaf proxy" turns up some stuff that looks relevant.


-KenFerry

----

NSInvocationBuilder is probably a root class for the same reason that NSProxy is a root class: to catch all messages with     forwardInvocation:.

*I don't see why it shouldn't inherit from NSProxy.*

NSProxy implements more methods than strictly necessary. It's a good base for many things, but an NSInvocationBuilder that inherited from NSProxy would not (without manually overriding those methods) be able to catch calls to anything in the NSObject protocol.

----
**DEFINING YOUR OWN ROOT CLASSES**

This is very simple. Just create a class that a) does not inherit from anything, b) implements the NSObjectProtocol, and c) defines NSObjectProtocol<nowiki/>'s message isProxy and returns TRUE.

    
@interface MyRootClass <NSObject> {
}

@end

@implementation MyRootClass

- (BOOL)isProxy
{
    return TRUE;
}

@end


Simple, eh? While you're at it, try implementing the retain/release/releaseCount system that you also find in NSObjectProtocol, or the self message that is also there. (this is probably not as easy) - PietroGagliardi

----
Actually it's pretty easy. Reference counting can be implemented using NSIncrementExtraRefCount(), NSDecrementExtraRefCountWasZero() and NSExtraRefCount(). The self method should be as simple as "return self;", and the -class method should be something like this (when not taking care of meta classes):
    
- (Class)class {
  return self->isa;
}


- OfriWolfus

