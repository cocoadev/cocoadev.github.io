---
layout: page
title: HowToDeclareAbstractClasses
---


Well, I suppose this is an easy one, but, don't know why, i haven't found it anywhere.
I need an abstract class but I don't know how to declare it (something like "public abstract class className" in Java).
Thanks in advance

----

Simple answer: there is no such thing as abstract classes in ObjectiveC.

Complex answer: if you really need to have an abstract class you can implement one of its methods as     [self doesNotRecognizeSelector:_cmd] or     [NSException raise:NSGenericException format:@"Method %s is abstract", _cmd] or something like it. It won't stop anyone from instantiating the class, but it will stop them from using it, without overriding that method. --TheoHultberg/Iconara

----

Also see AbstractSuperClass. You might be able to use FormalProtocols.

----

Classes in Objective-C are abstract by contract, not by any language feature.

----

OK, Thank you. I guess I can leave that class as it is and it'll be abstract only conceptually

----

I've said it before, and I'll say it again: ObjectiveC really needs proper access modifiers and support for things like final and abstract classes and methods.

--TheoHultberg/Iconara

----

We don't need abstract classes: we have protocols. Also, "final" was added to Java because many web applets have to extend various classes to implement their functionality, but if they can override *anything*, that introduces a security hole large enough to park an iceberg though :)  We don't need final methods: we don't run untrusted code like Java does.

Why do you feel we need these tools? -- KritTer

----

Abstract classes are good since they they make the contract obvious. I can agree that it's just syntax sugar, but it's very good to be able to have compile-time-checking that you follow the contracts (that's why I really like ObjectiveC's static typing, even though it's just syntax sugar). If the designer of a framework intended the class to be abstract, I think it's very good that the compiler tells me so. Moreover, abstract classes are more powerful than protocols, or rather, they solve another problem. An abstract class can contain logic with empty boxes, which the client programmer can fill in, as in TemplateMethod DesignPattern, a protocol cannot. Protocols are good for other things. 

Final is good for the same reason as abstract classes: a message that can't be missed: don't override this method! It is a useful feature of the Java language, for more things than applet access-control. If my design requires the exact implementation of a method, I mark it as final. If I have a class that should never be subclasses (since it would create strange and undefined behaviour), I mark it as final, nice.

--TheoHultberg/Iconara

----
It's not indispensable, though, unlike in Java.

As to using abstract classes as a template, why not use a delegate object?
-- KritTer

----
That's another solution to the same problem, it's very elegant in ObjectiveC, but it's not an argument for not having real abstract classes. 
--TheoHultberg/Iconara

----
See TemplateMethod for how to use a delegate object to implement this DesignPattern.
--AndrewBowman

----

You can just override the designated initlializer of your code and go 
    
if (self class] isEqualTo:[[[AbstractClass class]]) { 
    NSLog(@"Error, attempting to instantiate AbstractClass directly."); 
    [self release]; 
    return nil; 
}


----

Wouldn't it be better to raise an exception rather than just log an error?

    [NSException raise:@"Abstract Class Exception" format:@"Error, attempting to instantiate AbstractClass directly."]

*as stated above...*

----

Also, see WhyDeclareClassAsAbstract

----

Just use a FormalProtocols.

