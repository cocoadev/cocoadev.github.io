---
layout: page
title: BindingsBehindTheScenes
---



How are bindings implemented?

So far this is what I have been able to figure out.

When you observe an object, by using     bind:toObject:withKeyPath:options: then the     isa pointer of the observed object will be changed to a new class which in every way resemble the original, except that it has another name "NSNotifying_<original name>", and the method table is different.

The method table of this new class contain:
    
_isKVOA
dealloc
class
classForCoder
description
isKindOfClass:
isMemberOfClass:

I would have thought it contained overwritten versions of     setXYZ: which would wrap the original implementation in     willChangeValueForKey:/    didChangeValueForKey: -- but apparently not.

*Apparently it does, in a way... that is, if I iterate the     isa->methodLists the above is all I get, but if I send     set<Key>: and then dump the cached methods, there is an entry for that method, pointing to     NSSetObjectValueAndNotify (a private function from AppKit).*

**Finally figured it out (by use of GDB), the     methodLists of the     Class structure is always an array, even when the     CLS_METHOD_ARRAY flag is not set (contrary to documentation)!**

There is a private     _isKVOA method which return a BOOL -- I am thinking that the run-time system (i.e. objc_msgSend) queries this method to see if the object needs special treatment.

There is however some loose questions: where is the old     isa stored? The new class is not a subclass of the observed class (but a sibling to it), so for method forwarding it would need a pointer to its sibling (and the new class stores the original class's meta object in its     isa, so it cannot be stored there).

Does objc_msgSend cooperate with the system for it to work? Binding support is in AppKit only (i.e. if you only link with the Foundation framework, you cannot bind values together), so having binding support in objc_msgSend seems wrong.

Will it fail if I use     methodForSelector: and change values through the implementation pointer? likewise, if I use CoreFoundation functions to change values, will it likewise fail (to notify observers)?

If I observe several values in an object, the binding system will only use the single     isa replacement, so who knows which values are observred for individual objects? Is there a central repository of these things?

*It turns out that this part is actually in the API, which contain     observationInfo, although it returns     void*.*

**And well, is probably not used -- as I stated above, only the values being observed get new implementation pointer, so no bookmarking is required apart from this. This means that if you notify key a, b, and c of object P and key d of obj Q, then both P and Q will invoke     willChangeValueForKey:/    didChangeValueForKey: for all of a, b, c, and d.**

So far my thinking is that objc_msgSend is doing something like this:
    
id objc_msgSend (id obj, SEL method, id value)
{
   // of course looking up the methods manually,
   // as we would otherwise get infinite recursion
   if([obj performSelector:@selector(_isKVOA)] == YES)
   {
      id obs = [NSObserverManager sharedInstance];
      Class isa = [obs originalISAForObject:obj];
      NSString* key = objc_keyNameFromSelector(method);
      if(key && [obs isKeyObserved:key forObject:obj])
      {
         [obj willChangeValueForKey:key];
         objc_msgSendViaISA(isa, method, value);
         [obj didChangeValueForKey:key];
      }
      else
      {
         objc_msgSendViaISA(isa, method, value);
      }
   }
   else
   {
      objc_msgSendViaISA(obj->isa, method, value);
   }
}

I left out handling the case of     setValue:forKey: and     setValue:forKeyPath:, so in addition to calling     objc_keyNameFromSelector() it should use the second argument as key if the selector is any of the two I just mentioned (but I did not write my objc_msgSend as a var-args, so only one argument present!) -- although these two selectors could be handled by NSObject, that can be checked by overwriting them and see if notifications are still sent when the messages are sent to the object.

One problem with the above "theory" is that I tried to overwrite     _isKVOA (to print whenever it was called), and it was never called... however, NSObject has a default implementation that returns NO, and objects to which observers are bound returns YES. But maybe it is used in another context.

*Okay -- I spent some time with GDB, and my conclusion that objc_msgSend was in on the deal was wrong! objc_msgSend does find an implementation for the     set<Key>: and it is     NSSetObjectValueAndNotify, something supplied by AppKit.*

----

I'm not surprised by that. I wouldn't have expected Apple to customize objc_msgSend just to support CocoaBindings or KeyValueObserving. Also note that I think that **NSNotifying_�** class is only created if the original class returns YES from **automaticallyNotifiesObserversForKey:** (which it does by default).

I'm curious what creates the NSNotifying_� class. Is it NSObject's **initialize**?

�DustinVoss

----

Yes, I did not expect objc_msgSend to be modified myself, but when I could not find the old methods, this was my only sane theory.

With regard to whom create the new "proxy" then there is a lot of (private) classes in the system related to bindings, some of them I think are used to setup the actual bindings, so it could be one of these.

NSObject's     initialize probably won't do it, because there is a different proxy for each observed class, and initialize is only invoked once (unless a subclass passes the message to super).

And I agree that when     automaticallyNotifiesObserversForKey: returns     NO, then the proxy is most likely not created, as this is the sole purpose of the proxy (to automatically notify observers).

----

What are the superclasses of the NSNotifying_x class, and what methods do they have? There could be a superclass, like NSNotifyingBase or something, that provides most of the actual support for this.

-chris reed

----

The superclass is the same as the object which is "replaced". So basically it is creating a new class completely identical to the old one except for the name and some new methods, where the new methods make out the primary methods, and the old methods exist as a category for this new class.


----
according to http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/9objc_runtime_reference/chapter_9_section_55.html , objc_class has a flag "CLS_NEED_BIND (0x80L)". Not sure if this is useful info though.

----

I would think that it is a coincidence that the flag is named something with bind, since the flag value indicates that it is not the last added flag -- perhaps someone with pre-Panther documentation could check if the flag is also present in their development kit. Here is what I get with Panther includes:
    
[11:56][~]> grep BIND /usr/include/objc/objc-class.h
#define CLS_NEED_BIND           0x80L
 * CLS_NEED_BIND: load, initialize


----

*Clarification:     CLS_NEED_BIND is about binding as in the runtime phase of linking. It indicates that the class hasn't been fully loaded yet, i.e.     +load and     +initialize haven't been called.*

----
I am trying to understand the mechanics of this as well. I think that this is the best strategy to support WeakPointers as well. Perhaps, some folks here might have some insight that would help me there and vica-versa.
----
Could you be more exact? How do you need to support WeakPointers? and how is it that you think bindings may help implement it?

Update: Okay, I read the WeakPointers link. Yes, the same technique could be used. I.e. with MethodSwizzling the function pointer is swapped for the class and with the binding-stuff it is the isa pointer of the object which is swapped.  However, the swapping of isa pointer can sort of only be done by one system.

E.g. if you bind to an object, the isa is swapped, if you use it as a WeakPointer then the isa is again swapped, if you then unbind, the isa is swapped back, but the wrong one, and then when you no longer use it as WeakPointer, it will again be swapped back, but again to the wrong pointer...
----
Ahhh... Good point! One would have to be most careful around previously swizzled; which begs the question (among many) "how can you tell". Nonetheless, I have yet to get an implementation of "CreateASubclass" that works. AND, weak pointers still have their uses.
----
What is the goal? To have a weak pointer which automatically gets set to nil when the object it points to is deallocated? e.g. by having the weak pointer actually point to a proxy which will forward the methods as long as the original object lives.

You write that you cannot subclass and override     dealloc to send a notification (or similar), but why not? is that because of the performance penalty involved in having all objects send this notify (e.g. checking a dictionary first to see if they are currently the target of a week pointer)?

Let me add that currently reference counting is implemented by having one giant dictionary of all objects which gets checked in every     release method, so performance is not dramatically affected by overriding     dealloc to do the above, and instead of a dictionary you could add a boolean to the instance data.

Is there a particular context in which you need week pointers, or are you just trying to come up with a general solution to the problem?
----
Weak pointers are not an uncommon pattern. Specifically, I have been working on a persistence mechanism and also a cache manager which would work nicely given a "Will dealloc" notification. It would also make a "view" oriented observer easy as well; imagine an object "disappearing" from view as its removed from the system. It seems a natural extension to the Binding framework. A pity they have chosen not to include it.

I'm coming to the opinion that isa-swizzling should be offered by a standard set of methods on (NS)Object; something like a -becomeClass: so that previously swizzled classes can be swizzled again. Not having a well known hook into the change is bad magic.

With regard to the reference counting performance; I agree in general but I'd prefer better performance if it can be had. A bit off topic but I have thought about hacking the     alloc and     dealloc to hide the reference count at the "front" of the structure. From many accounts; handling your own retain/release counts gains not insignificant performance. (That should be a new thread if folks are interested.)

So has anyone actually figured out how to create a true subclass dynamically? I can create one, even add working methods to it but calls to inherited methods cause problems.

-jason
----
I think your code is fine, the problem is with the way Apple has implemented class clusters -- try e.g. to use NSError instead of NSString and your code *will* work.

Interestingly I have two Open/Analyze bug reports relating to strings used in bindings. One cause a segmentation fault and the other is making the application hang, and I think what happens in the second is very similar to your scenario, i.e. a string is created with     initWithString: or similar, which is set as a value that the view is observing (thus the view will add an observer to this new string, i.e. create a dynamic subclass) and it just hangs...

The problem does not exist if a constant string is used, and indeed if I change your program to use just     str1 = @"string 1" instead of the     alloc/    init it will work (except that the string is never released).

I think you should file a bug report on this!

Come to think of it, it might be related to CoreFoundation bridging (since these might not be true ObjectiveCee classes) and not ClassClusters. Not sure...

Update: Yes, the problem is with CoreFoundation objects. Create a dynamic class with NSString as super class and send the class an     alloc and then     retain to the object -- works fine. Now change the superclass name to NSCFString and it will hang when sending it the     retain message.

I have not been able to find out exactly which methods cause the problem, as it is not all of them. My first though was that it had to do with the instance data, but I can't really make sense out of it...
----
Yep! I've experiencing the same thing. I have to double-check my example with the example code posted in WeakPointers but I was able to get it to work with my own classes but NOT with NSString. Will submit the bug.

-jason

----
Submitted the CoreFoundation string bug (as exhibited in my WeakPointer example). The example code has been updated and works (VERY limited testing) excepting the aforementioned bug.

-jason

----

It would be great if we could establish a generic message flow of how this all takes place, as we began to do above, but maybe in bullet format going through just about every step we know about from a change in a content object to the controller to the view, and vice versa, such as  "setValue:forKey" -> calls set<Key> -> actually calls NSNotify_... etc.  I was also wondering how it is that an infinite loop is not begun when changes are made, as a view will trigger the controller to trigger setValue which will again trigger the controller to change the view which triggers the controller to change the content... and so forth.  Does the controller not use the NSNotifying_ subclass? or just ignore subsequent notifications?

