---
layout: page
title: GetTheSizeOfAnObject
---



Just want to know how to get the memory footprint of an object.

The code below prints "size of a NSObject: 4"
    
NSLog (@"size of a NSObject: %i", sizeof (NSObject));


Even according to Foundation/NSObject.h it is 4 bytes:

    
@interface NSObject
{
	Class isa;
}
...


but I know it can't be (i.e. what about the retain count and other stuff which is private???)

Is there any way to determine exactly the footprint of any object? Thx

-- peacha

----

Because all you are really checking with the function sizeof is the size of the pointer to the object.  So 4 bytes is correct for that.  The size of the object itself has its size checked using a different method.  -- DavidKopec

NSObject is not a pointer, it is the object itself. id is a pointer. -- KritTer

----

KritTer is right. If you want a proof:

    

@interface MyObject : NSObject
{
	id anID;
}
@end

void showMe ()
{
	NSLog (@"size of MyObject: %i", sizeof (MyObject));
}



gives "size of MyObject: 8"

Back to the question: I haven't found find any class methods or whatever giving the size of an object... Any help?

-- peacha

----

Alas, even the objc runtime agrees with sizeof. The following:

    
    NSLog(@"NSObject size: %d",
          ((struct objc_class *)[NSObject class])->instance_size);
    NSLog(@"NSObject ivars: %d",
          ((struct objc_class *)[NSObject class])->ivars->ivar_count);


gives "NSObject size: 4" and "NSObject ivars: 1". ("ivars" = instance variables")

I assume the retain count is kept elsewhere, perhaps by the NSAutoreleasePool class.

--KritTer

----

So it seems like I can trust sizeof...
Thx -- peacha

----

I don't know for sure how it's implemented by Apple, but I can tell you how it's implemented in GNUstep. Here is a small part of the NSObject code :

    
/*    Copyright (C) 1994, 1995, 1996 Free Software Foundation, Inc.
 *    This file is part of the GNUstep Base Library.
 *    Released under the GPL v2
 */
- (id)retain
{
  NSIncrementExtraRefCount(self);
  return self;
}

void
NSIncrementExtraRefCount(id anObject)
{
  if (allocationLock != 0)
    {
      objc_mutex_lock(allocationLock);
      ((obj)anObject)[-1].retained++;
      objc_mutex_unlock (allocationLock);
    }
  else
    {
      ((obj)anObject)[-1].retained++;
    }
}


So the retain count is kept 4 bytes *before* the actual position of the 
pointer. Why is it done like this ?

Partly to support garbage collecting. It lets you add garbage collecting without changing the interface of any class, you would just need to change the alloc/retain/release/dealloc methods of NSObject.

GNUstep does support garbage collecting for the base library (the equivalent of
the FoundationKit), but I never tried 'cause it doesn't work for the gui part.

--PierreYvesRivaille

