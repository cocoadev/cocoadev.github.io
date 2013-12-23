---
layout: page
title: NSNotificationMultipleObjectDispatch
---

Hey,
I am writing an API where objects can attach and detach with one another. A notification is sent out upon a finished attach or detach. Since two distinct objects are involved in the communication I wanted to have filtering based on both objects, rather than one, sorta like this:

    
[myNoteCenter postNotificationWithName:@"myObjectsDidAttach"
    object:[Either object:obj1 orObject:obj2]];


So that listeners (er, observers) can receive a notification if they are watching obj1 OR obj2. So my questions are, does NSNotificationCenter filter based on pointer value (as I suspect) or is there an isEqual: call made? Much more importantly, has anyone else encountered such a requirement and solved it already :)

Thanks,
Jeremy Jurksztowicz

----

It filters using isEqual.  Alternatively, you could send a notification for each object involved in the attach/detach.

You should assume all object equality comparisons in cocoa are isEqual comparisons unless you have reason to think otherwise.

Note that if you make a custom class ("Either" in your example) and override     isEqual then you need to override     hash as well.

----

Uh oh, does this mean that Either has to have the same hash value as both obj1 and obj2? Having obj1 and obj2 with the same hash value smells like trouble to me. Do you think it is necessary to override hash in this example? Also in the example, will it be     [eitherInstance isEqual:obj1] or     [obj1 isEqual:eitherInstance]? Is it even defined which will be called? I know they should be identical, but I am writing a hack, which will only be used in NSNotificationCenter. Right now posting two notifications is sounding better.

*Taken **directly** from Apple's docs: "One basic invariant for objects that are put in a hash-based Cocoa collection such as an NSDictionary or NSSet is that if [A isEqual:B] == YES, then [A hash] == [B hash]." So making it equal to both objects is probably a bad idea, because you won't know which hash to use. You can't ignore this rule because NSNotificationCenter might use a "hash-based Cocoa collection". And if it **is**     [obj1 isEqual:eitherInstance], you can't fix this without redefining     isEqual: for all objects, either through MethodSwizzling or categories. Better to stick with with two notifications, unless you would rather have an object that catches all     myObjectsDidAttach notifications. --JediKnil*

