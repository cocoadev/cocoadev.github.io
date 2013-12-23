---
layout: page
title: NonObjectiveObjectiveCee
---

Is this like using printf() and system() instead of NSLog() and NSTask??? *More like non-dairy cheese than anything.*

No that is using C from Obj-c. You can't have NonObjectiveObjectiveCee or you have c.

*Just a little nit to pick, NSTask is equivalent to popen() not system().*

**Since ObjectiveC is a very minimal syntax addition to C which deals only with OO, NonObjectiveObjectiveCee is a contradiction. One could, however, possibly use the ObjectiveC runtime for things that are not OO, I guess. By why?**

**On the other hand, if by "objective" you mean full-scale object oriented design, it's quite common for newbies to have a class or two and do all of their logic in them, and that is not OO.**

That is also OO, it's also shoddy design.

*No, it's not OO, if OO doesn't mean "code that uses objects", which I wouldn't agree with. OO is about delegating responsibility to classes, encapsulating data and things like that, not putting imperative code into a class or two.*

*And NSLog() is definitely not OO..*

*NSLog() is as OO as it should be - it supports arbitrary objects via %@. Stream interfaces like in C++ look cute at first, but are really a mess... and imagine doing it without the operator overloading.*

**Imagine how to extend NSLog into printing arbitrary data structures like NSRect, NSSize, NSRange etc. -- it can't, end of encapsulation, NSLog is not as OO as it could be, though whether that's actually a disadvantage is another question. C++ streams are very stupid OO, modern C++ generally ditch the inheritance part of OO.** <-- and virtually every other part, save for encapsulation

*NSRect and NSSize aren't OO either, so not being able to print them doesn't make NSLog less OO...*

    NSLog(@"size: %@", NSStringFromSize(theSize));

    NSLog(@"size: { %i,%i }", theSize.width, theSize.height);

Well, in a true OO language (like Java or SmallTalk), NSRect and NSSize would be objects. Probably Apple figured that pass-by-value worked a lot better, but it does require a slightly different mindset that's less OO. --JediKnil

In a true OO language (like Java) int and long and double would be objects. Oh wait, they're primitives in Java. Mostly. Sometimes we care about performance, not just OO purity. And don't forget that Java beans (regular ones) are effectively just structures. *All right, I'm not getting into this here.*

*
Let's get into it just a bit: if a class is just a bunch of getters and setters, with no logic, like DTOs or basic Java beans, is it an object? You could remove those getters and setters and access variables directly with no functional difference and maybe a performance increase. We claim that encapsulation is important, but if it really was we wouldn't let outsiders be setting and getting all those variables. So a structure becomes an object if we simply add accessor methods?
*

Getters and setters access attributes, which may or may not be implemented as fields, and whose implementation is subject to future revision. That's OO.
Also, object-orientation is a design issue, not an implementation technicality. Carbon, for instance, is largely object-oriented in design, despite not using any language's class mechanism.

You can also use c++ objects in objective-c++, for true Rect and Point objects, I logically seperate my obj-c code into objects (obj-c) and microcomponents (mostly inline c++). Besides vector<id> or set<id, object_compare> make really good containers.

*Or you could write your own wrappers for Rect and Point in ObjC.* (the optimal solution if optimal performance is not an issue).

The optimal solution would be to use the wrappers already provided for you, namely NSValue.

*Gotta love when people use the word optimal like that...*

NSValue is not the height of OO design mind you, I like to have some type information with my objects :)

*NSValue does contain type information, in the form of a ObjC encoded type string.* Which tells you that it is an NSValue, not a rect or point.

