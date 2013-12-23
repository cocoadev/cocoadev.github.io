---
layout: page
title: ObjCDesignPatterns
---

Let's create an Objective-C Design Patterns repository ala: the PortlandPatternRepository .  If you have a design pattern for Obj-C or Cocoa, create a page for it, and then add it here.

See also: BadSmellsInObjC, DesignPatternsElementsOfReusableObjectOrientedSoftware, PortlandPatternRepository

----

SingletonDesignPattern

ModelViewControllerPattern

CreatingObjectThatWillUseADelegate (DelegationDesignPattern)

*neither of these is specific to Cocoa or ObjectiveC as the name of the page would imply*

isn't the entire concept of a language-specific DesignPattern an oxymoron?

Even though the core *pattern* should be fairly language independent (or just depending on the language class -- ie:  OO and/or imperative), there are language-specific implementations of the patterns into *sub-patterns*.  For example, ClassClusters are a pretty Obj-C specific (or tied to any dynamic OO language which doesn't make constructors special) even though it is really just a re-branding of a factory with some extra goo added.

----
I think the Cocoa Class Clusters Pattern is the GOF Facade Pattern plain and simple.  A single class provides a simplified interface that conceals/encapsulates a more complex interface or implementation consisting of multiple classes.

Or �Provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level interface that makes the subsystem easier to use. This can be used to simplify a number of complicated object interactions into a single interface. �

See also: AbstractFactoryPattern, MediatorPattern
----

ClassClusters have almost nothing to do with a facade.  You only end up with one class, which class it is is the interesting part.  That is the behaviour of a factory.  The fact that all resultant classes share a common interface is not really an aspect of the design.  In the end, you are still only working with one class at a time.  The only real way in which it differs from a tradition factory is that it uses the normal initialization routines so it doesn't appear, at first, to differ from usual object initialization.

----

VisitorPattern -- discussion on how to implement the VisitorPattern in ObjectiveC

----
Strategy pattern is not possible in cocoa or objective c? If it is possible can any one tell me how to accomplish this? How to call derived class's method (overriden) using base class pointer? For example say i have overriden init method of a class derived from NSObject. Now how to call derived class init method using NSObject. that is   SubclassNSObject *snso= [[NSObject alloc] init]. -----> this will call NSObject init method
----
You seem to be very confused.  The Strategy pattern is easy with Objective-C as it is with any language that supports polymorphism.  See http://en.wikipedia.org/wiki/Strategy_pattern for an explanation of the Strategy pattern.  You can implement the Strategy pattern in Objective-C in exactly the way the UML diagram provided by wikipedia shows.

For just one example: In Cocoa, the NSLayoutManager class uses the Strategy pattern via the NSTextContainer class and its subclasses so that the "strategy" the for laying out text into arbitrary shapes can be changed/controlled at runtime.
----
Yes. i made mistake above. will be looking into ur links. sorry. Is virutual functions are supported in objc? can we use base class pointer to invoke subclasses method? 
----
Yes.  All Objective-C methods are "virtual" (to borrow a C++ term).  Objective-C uses polymorphic message dispatching so that messages sent to a pointer to a base class invoke methods implemented by the actual class of the receiver.  In fact, language level type information is irrelevant at run-time in Objective-C.  Any message can be sent to any receiver.  The Objective-C id type tells the compiler that the type of a symbol is "any object". 
----
Perhaps you should read some very basic information about this language before you start trying to use it for fancy design patterns....
----
In above mentioned site [ http://en.wikipedia.org/wiki/Strategy_pattern ] strategy pattern is explained in many languages (ruby, cpp , c#) but objective-c lang is missing. Can any one make some time and do it objc lang also? really it would be nice, and helpful for others like me. May be why i am confused about strategy pattern is- There is no abstract class in objc. most of the strategy pattern is programmed to interface or abstract class. they use abstract class pointer to invoke derived class method. i am not finding a way to create similar class structres as mentioned in wikipedia site. can any one tell me how to do it?
----
It would help to read the Wikipedia article more carefully, but basically, the strategy pattern allows one object to know only what another object does, not how it does it. Then any object that can *conforms* to that set of actions can perform it (when you see "conforms", think FormalProtocols, at least at first -- see HowToDeclareAbstractClasses). The C++ summary based on sorting (not the code but the explanation) is pretty apt.

Interestingly enough, Cocoa seems to use selector-level strategies quite often as well, such as NSMutableArray's     sortUsingSelector:. I suppose this comes from the ease of adding methods via categories...what do experienced programmers have to say about this? What are the pros and cons of selector/method-level strategies and protocol/NSCoder-type-abstract/ClassClusters-level strategies? (And should the discussion be moved to a new page?) --JediKnil

----
Why should we feed you information on how to implement Strategy when apparently you don't even know the very basics of the language? You need to do some of your own work. If you have a specific question then feel free to ask it, but at this point you're basically asking us to teach you the language.

You should read *The Objective-C Programming Language*: http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/

