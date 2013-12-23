---
layout: page
title: KeyValueObservingArray
---



I'm having a little trouble using KVO in my code. I'm not using bindings here, the purpose of it is really to handle undo, where the observer sends the observed old value up to the undo manager (basically the code in Hillegass, pp 147-149). Let's say I have a class <foo> that has a few properties of its own. I can add observers of those properties and all is fine. In addition, <foo> has an array of further sub-objects, let's call them <bar>. I'd like to be able to observe changes to these too, so I tried:

    

[self addObserver:object forKeyPath:@"arrayofbars.barproperty" options:NSKeyValueObservingOptionOld | NSKeyValueObservingOptionNew context:nil];



Which follows the same pattern as I use for adding observers to foo's own properties (here, self is referring to a foo object). It also appears to follow the usual rules for KVC keypaths on arrays. At runtime, I get an exception:

[<NSCFArray 0x592a10> addObserver:forKeyPath:options:context:] is not supported. Key path: barproperty

Is this to be expected? According to all the docs I can find, as long as you are KeyValueCoding compliant, which as far as I can see I am, then KVO should also work. If not, why not? And more usefully, what is the correct way to add an observer for objects contained in an array? I also had a look at:

    

self arrayofbars] addObserver:object toObjectsAtIndexes:ix forKeyPath:@"barproperty" options:[[NSKeyValueObservingOptionOld | NSKeyValueObservingOptionNew context:nil];



And that at least didn't complain. However, this seems to be really just a shortcut for adding observers directly to the objects in the array. What if I add new ones or remove some of them individually? It would be nice if there were a way to simply get observations on these without having to worry about setting them up individually and also removing the observers at the right time, which turns out to be tricky. --GrahamCox

----

You can't bind directly to the general idea of "all an array's members" like NSArrayController does without going through the controller layer. From what I can tell, NSArrayController internally handles observing all the objects in the array as well as the array itself, and it automatically adds or removes objects from observation as they come and go from the array.

----

There's some misunderstanding there.

It's not true that KVC compliance implies KVO compliance.  When _you_ implement an object, then if you always modify ivars by going through accessor methods, and if your class automaticallyNotifiesObserversForKey: (which it does by default), then you become KVO compliant for free.  

However, you can never assume that something whose implementation you do not control is KVO compliant unless you're explicitly told.  Almost no properties in the AppKit are KVO compliant, for example. (KVO compliance is also not required for something to be bindable.)

In the case of NSArray, lack of KVO compliance is a design decision.  NSMutableArray is a fundamental workhorse data type in Cocoa.  If it was threadsafe, if it was KVO compliant, you would pay for these features in code that doesn't require them.  Instead code that wishes to be usable from multiple threads, or that wants to have observable to-many properties, must add those features externally to NSArray.

Think of the NSArray in this case as just storage, like a C-array.  What is being observed is a to-many relationship on your object, not the storage of the data.  See Malcolm Crawford's examples for what this means in practice, this is just the high level picture. 

What's confusing is that NSMutableDictionary _is_ KVO compliant despite similar concerns.  Oh well.

----

Thanks for the explanations. I guess what I'm looking for is some insight into how to best handle dealing with observers when adding and removing objects from my array. The problem seems to be that the objects themselves don't have any concept of who is observing them or why. If an observed object is removed from the array and released, error - deallocation without removing the observer. (Exacerbated by there not being a generic -removeAllObservers method that I could call from dealloc). If a new object is added, it goes unnoticed. Both situations are unnacceptable. The object that owns the array ("foo") might know what to do, but the objects ("bar") themselves don't. I'm thinking that a subclass of NSMutableArray might do it, so I can hook into -addObject: and -removeObject: (effectively my subclass can become KVO-aware where the standard class is not). Does that sound like the right way to go, or is there a more obvious solution I'm overlooking? --GC

