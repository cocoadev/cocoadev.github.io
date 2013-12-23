---
layout: page
title: AutoReleasePool
---



To understand any of the stuff I'm talking about here, you should be familiar with the basic concept of RetainingAndReleasing objects.

In that article, we talked about how sending AnObject the autorelease message would not actually release that object right away, but instead keep it around a little longer before it goes away.

This is something that's desirable when you want a method to release AnObject it returns, but want to give the method's caller a chance to retain the returned object.

The thing that's working behind the scenes to make this happen is the AutoReleasePool.

As soon as you call the autorelease message on AnObject, Cocoa creates another object called AutoReleasePool that will retain the object for a short time before releasing it again automatically. That's what bridges the gap between the release from the creator method and the (possible) retain from the caller.

This involves a lot of work, so to avoid sluggish performance you should only use it when you really need that AutoReleasePool to be created.

The good thing is: You don't really have to do any work to get this done; the system normally automatically takes care of everything directly related to the AutoReleasePool. You can, however create your own AutoReleasePool for any given context. If you do, all the autorelease calls in that context will automatically use that new AutoReleasePool.

More on this (including examples) can be found in this article:

http://www.stepwise.com/Articles/Technical/HoldMe.html
----

Some of the information in this page is incorrect.

Cocoa does not create an AutoReleasePool when you call     [object autorelease]. It uses the **most recently created** pool in the current thread. They are stackable, so that you can create as many AutoReleasePool's as you like and it will always use the most recent one.

You can think of an AutoReleasePool as a simple list. The list contains objects to be released later. When the AutoReleasePool object is itself deallocated, it releases all the items that it's taken charge of.

AppKit usually takes care of making sure that there's always one around. When you call     [object autorelease], no new pool is created. It just looks up the current pool. So it's approximately this:


* Get the AutoReleasePool that was most recently created in this thread
* Add     object to that pool's list of items to release


"This involves a lot of work" isn't really true ...     [object autorelease] isn't trivial but it's not allocating any memory either. Sometimes convenience and ease of development is the most important thing. Don't start hacking at your code until you can actually find and prove what's -really- making it slow ... watch out for PrematureOptimization!

(Some supporting evidence to show that     -autorelease doesn't actually create an NSAutoreleasePool object can be had by creating a FoundationTool and not creating an NSAutoreleasePool manually. You'll get warning messages whenever anything is autoreleased, because there are no autorelease pools in the stack, and the memory will be leaked.)

When do your objects get cleaned up anyway? When the pool object is destroyed, it releases all of the objects that it's been given. It does this from its     -dealloc method. The pool is intended to be destroyed by the same code that created it ... which makes everything stay balanced. Normally you use the pool created by AppKit around the event handler, which conceptually is something like this:

    
 NSAutoreleasePool *pool = NSAutoreleasePool alloc] init]; // pool is created
 
 [self handleEvent:event]; // your methods get invoked somewhere in here,
                           // and you will probably add objects to this pool
 
 [pool release]; // all your autoreleased objects are released


--[[DrewThaler

----

Regarding performance. Yes, you should try to avoid autoreleasing in a tight loop, which can cause you to accumulate tens of thousands of objects into a single autorelease pool. Frequently you can avoid this by managing the releases a bit more carefully, or using mutable objects when possible. For example, this is wrong:

    
 // WRONG: This code fills up the autorelease pool with 100,000s of dead strings
 // which get larger and larger. Later, when the pool is released, your app
 // will freeze for a while as it chews on all those objects.
 NSString *string = @"";
 int i;
 for (i=0; i<100000; ++i)
 {
    // next line creates two autoreleased objects
    string = [string stringByAppendingString:[NSString stringWithFormat:@"%d", i]];
 }
 return string;


One alternative might be to use alloc/init instead of autorelease:

    
 // BETTER: This code only uses the memory that it needs, and cleans up
 // unneeded objects quickly.
 NSString *string = @"";
 int i;
 for (i=0; i<100000; ++i)
 {
     NSString *newString = NSString alloc] initWithFormat:@"%@%d", string, i];
     [string release]; // release old string
     string = newString; // keep new string around
 }
 return string;


Another solution is to create an autorelease pool inside the loop. For this example we release it every time through, but if you were clever you might want to release it only every 1000 times through the loop or so.

    
 // BETTER: This code creates and releases its own autorelease pool 
 // each time through the loop. Dead objects are cleaned up right away.
 // This is not as efficient as the above method, but it might be necessary
 // depending on what you're doing inside the loop.
 NSString *string = [[NSString alloc] init];
 int i;
 for (i=0; i<100000; ++i)
 {
     // - - -
     // if we were to autorelease an object HERE, it would go into
     // an autorelease pool somewhere above us, probably created by [[AppKit.
     // - - -
 
     // create our own pool.
     NSAutoreleasePool *pool = NSAutoreleasePool alloc] init];
     
     // - - -
     // if we autorelease an object HERE, it will go into our pool.
     // - - -
     
     // next line creates two autoreleased objects, which go into our pool.
     NSString *newString = [string stringByAppendingString:
                                       [NSString stringWithFormat:@"%d", i;
     
     [string release]; // we don't need the old string object anymore
     [newString retain]; // increase retain count, otherwise the pool will release it!
     string = newString; // swap pointers
     
     [pool release]; // this line releases the autoreleased objects, and
                      // removes the pool from the stack.
 
     // - - -
     // if we were to autorelease an object HERE, it would go into
     // an autorelease pool somewhere above us, probably created by AppKit.
     // - - -
 }


Yet another solution (which works well for this simple example, but may not be practical in real life) is to use mutable objects so that you're not creating so many temporary objects and copying memory around all the time.

    
 // BETTER: This code uses only one object which is dynamically resized, rather than
 // creating lots of temporary copies.
 NSMutableString *string = [[NSMutableString alloc] init];
 int i;
 for (i=0; i<100000; ++i)
     [string appendFormat:@"%d", i];


Notice that in all cases we've been talking about a tight loop. That's generally the only time you need to worry about autorelease performance issues. If you are not in a tight loop, or if you know that you have a pretty reasonable upper bound on the number of times you're going to loop, then you probably shouldn't worry too much about autoreleased objects since the performance difference will be negligible.

----

Here is a useful bit to simplify the release of  automatic variables: 

The gcc based objc-c compiler supplied from apple has a feature that is not widely known. Automatic variables (i.e. "locals") can be attributed with a cleanup function which is invoked when the variable goes out of scope. See this example: 
    
 // The cleanup procedure has one parameter, which must be compatible to a pointer to the value which is to be cleaned.
 void release_id(id *n) 
 {
  [(*n) release]; 
 }
  #define auto_released __attribute__((cleanup(release_id)))
 
 int main()
 {
  auto_released NSMutableString *s=[[NMutableString alloc] init]; 
 } // s automagically relased when falling out of scope
 


An obvious use case would be: 

    
 
 #define CONCAT(X,Y) X##Y
 #define GENLOCAL(X) CONCAT(__local__,X)
 #define with_autopool auto_released AutoreleasePool * GENLOCAL(__LINE__) = [[AutoreleasePool alloc] init]
 int main()
 {
  with_autopool; 
  // code with autoreleasing objects
 } 
 


The technique should also work with lock/unlock pairs or whatever else has scope lifetime.

----
This seems like a lot of trouble to go through to save a single line of code, is it really worth it? I would estimate the number of autorelease pools I manually create at approximately one per 5-10,000 lines of code.

