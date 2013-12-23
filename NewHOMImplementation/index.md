---
layout: page
title: NewHOMImplementation
---



Just wanted to let everyone know that i've created a new HOM implementation (BSD licensed). It's available at http://www.dpompa.com.
I wrote this framework because I was dissatisfied with all the other implementations I found. All of them just felt wrong to me...
Unlike other implementations, this one doesn't have a class which performs the prefix message with the suffix (the trampoline itself in some, and EnumFilter in MPWFoundation); instead, the trampoline just catches the message, and passes the invocation of it to its target (a single trampoline can catch as many messages as you tell it to, and then pass an array containing all the invocations). The target is the receiver of the prefix message (but may be any object, in theory...) which then does all the work completely by itself. The result of the process is then passed back using the invocation's return value.
I also liked the idea of recycling the trampolines (from MPWFoundation), and added a class which allows you to recycle any object you wish (as long as it's a subclass of either NSObject or NSProxy).

The following prefix messages (defined in the OOPSLA paper) are currently available:
collect, select, reject, each (anArray each] doSomething] calls -doSomething on all the objects of anArray), ifResponds, do, selectWhere and async.
I also added the following since I found them useful:

* mainPerformAndWait: - Performs the prefix message in the main thread and optionally waits for it to finish.
* mainPerform - Same as above but without waiting.
* ifNotNil - Performs the prefix message only if all its arguments are not nil.
* singleSelect - Same as -select but returns only one object (the first match).
* singleReject - You can probably guess that ;)
* singleSelectWhere - Same.


All the source is documented using headerdoc comments, and there are also some comments in complicated methods like -do.
Please let me know what you think,

- Ofri

Note: This implementation hasn't yet been tested in a finished app. I tested all the methods to make sure they actually work, and we also use this framework in [[ChatKit, but you should still consider this as a beta.

----

Looks like an interesting project, your design is different and quite possibly better.

How did you get around the problem with select/reject caused by the fact that the compiler will think the message returns a BOOL, but in fact returns an object? This persistent problem has been one of my deeper dissatisfactions with HOM implementations in general, and I have not yet seen a decent solution. I poked around your source a bit but couldn't find anything relevant. -- MikeAsh

----

Well, it' indeed a problem. Eventually, I plan to write a preprocessor that'll allow native HOM syntax, but in the mean time I use 2 macros to work around this: HOMEnableArgumentMessageCompatibility(method) (or HOMEnableArgumentMessageWithArgsCompatibility(method, identifier)) and HOMStaticMessageSend(receiver, prefixMessage, argumentMessage). The first one you put before an implementation context that's going to use your method. It then expands to
    
@interface HOMTrampoline (identifier)
- (id)method;
@end


After this, instead of doing something like NSArray *results = anArray collect] floatValue] (which will generate an error), you'll do [[NSArray *results = HOMStaticMessageSend(anArray, collect, floatValue). (And yes, you will get an array of objects containing the floats returned. Currently it's an array of HOMObjCValue objects, but I may change it to NSNumber/NSValue sometime)

Some of the most common methods you'll probably use with -select, -reject and -collect are already defined in the header of HOMTrampoline, so you don't need the HOMEnableArgumentMessageCompatibility() part.

- Ofri

