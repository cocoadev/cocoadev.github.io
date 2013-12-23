---
layout: page
title: UsingVariablesFromDifferentImplementations
---



How can i use a variable (General/NSString) "bam" from a different implementation called "bang"???

I tried something like this:
    
General/NSString *bam = General/[[NSString alloc] initWithFormat:@"bam - %@", bang.bam];


But no such luck. an anyone help me?
----
I have figured it out :)

I'm now using accessors....
----
Im pretty sure accessors/mutators are all you can use between objects.
----
Nope. If you have an object "foo" and what to access the string "widget", you can use good ol' C: General/NSLog(foo->widget);
----
Unless you're smart and prefix your class with @private. -- General/MikeTrent
----
Direct access of ivars always seems like a C++ solution to an General/ObjC problem, to me; perhaps that's oversimplifying, but there you go. -- General/RobRix
----
Direct access of ivars leads to fragile base class problems like the ones that cause C++ framework users and programmers headaches. If the organization of the ivars change in the accessed class, the accessing class must be recompiled as well. Which is bad. -- General/DavidRemahl
----
See General/NSKeyValueCoding. It adds a few methods for doing this to General/NSObject as a category.  -- General/AdamAtlas
