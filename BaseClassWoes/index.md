---
layout: page
title: BaseClassWoes
---

Just to try to learn more about objC I'm trying to "write" a base class, this crashes even through from what I can see it should work.

    @interface MyObject
{
	Class isa;
}
+ alloc;
+ allocWithZone:(NSZone *)zone;
- (NSString *)description;
@end

@implementation MyObject

+ alloc
{
	return class_createInstance(self,0);
}

+ allocWithZone:(NSZone *)zone
{
	return class_createInstanceFromZone(self,0,zone);
}

- init
{
	return self;
}

- (NSString *)description
{
	return @"exists!";
}

@end

Well... looks like you have a long way to go.  AFAIK you have to implement retain/release/autorelease, although the actual ref counting is done outside the instance.  dealloc is a must for sure.

*ref counting is done outside of the object for NSObject.  Since this is a root class, you have no way of getting at the table NSObject uses for ref counting (and it won't happen automatically or anything).*

----

Nah, you don't have to implement retain/release to be a root class, believe it or not.  But if you'd like it to interact sanely in **Cocoa** (not just ObjC), you should implement the NSObject protocol.  http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Protocols/NSObject.html

For an example of a root class that does not implement the NSObject protocol (or retain/release) see file:///usr/include/objc/Object.h .

----

It would be really helpful if you would say a little bit more than just "this crashes", like saying *how* it crashes.

Last I checked, a bug in Apple's ObjC runtime made it mandatory for every root class to have a     +initialize method. If that's still the case, this could be the reason for your problem.

