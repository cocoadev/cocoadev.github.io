---
layout: page
title: KVOObservationFailing
---

Greetings all,

I seem to be having a problem with a manually registered observation.

I have an object Obj with a key 'theArray' of type NSMutableArray.  In the nib bindings are set up such that an NSTableView is bound to theArray (NSArrayController -> NSController).  This all works fine.  

The problem lies in another object which has registered for KVO via the following line:
[Obj addObserver:self forKeyPath:@"theArray" options:0 context:NULL];

Now, when adding rows and the like via NSArrayController everything works fine.  The observe: function gets called and all is well.  However, if one changes the value of a field directly from the NSTableView or another bound NSTextField, the observe: method is not called.  The value of theArray changes, as seen with a manual NSLog(), but no KVO observe: gets called.  Does anyone have any ideas as to how i could get notifications of _all_ changes done through KVC?

Hope I've been clear,
JohnLaxson

----

You are registering for notifications of the array key. If I understand you correctly you modify members of this array, e.g.     theArray objectAtIndex:0] setValue:@"for" forKey:@"bar"] and expect a notification to be sent for the array key?

But this key do **not** change.

There is unfortunately no easy way to do what you want to, this is why I started to investigate on [[BindingsBehindTheScenes, because I wanted to add this functionality myself.

More precisely, when objects were added to the array, the array would observe all keys of this object and when one got changed, the array would send     willChange:valuesAtIndexes:forKey:/    didChange:valuesAtIndexes:forKey: to super, with the change kind set to     NSKeyValueChangeSetting.

The current array controllers will react on such a change (i.e. redraw the changed items if shown in a table view).

You can currently do the above "manually", the problem is that you cannot observe all keys of an added object, as you will need to use     addObserver:forKeyPath:options: to observe each value (which needs a key path other than "*").

----

So, if I understand what you're saying the NSArrayControllers are actually receiving value change observations from the NSArray object, not my root object that has the array as a key.  Further, one may not "magically" observe all keys like the NSArrayControllers do because you can't observe a keyPath of "*".

Hmm.  That kind of leaves one in a difficult position.  Outside of observing every (known) key of the NSArray, is there any way to just get value changed notifications?

----

I doubht it -- the problem is, the object will only generate a notify for keys which someone is observing, i.e. the run-time system will not add stubs to send these notifications before receiviing a "bind:key ..." message.

I.e.     [foo setName:@"bar"] does **not** send a notification. But if we do:
    [fud bind:@"name" toObject:foo withKeyPath:@"name" options:nil] and repeat the     setName:, then this will get to another implementation of     setName: which will do     [self willChangeValueForKey:@"name"], call the real implementation and then     [self didChangeValueForKey:@"name"] to super.

So observing all keys would require that stubs were added to all set-implementations.

