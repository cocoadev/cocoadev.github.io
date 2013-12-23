---
layout: page
title: AbstractSuperClass
---



An AbstractSuperClass is a class that defines some useful behaviours and/or interface for an object, but does not specialize. Generally, these classes aren't useful to you unless you subclass them and inherit the behaviours and/or interface, and then specialize those to your needs.

Also sometimes referred to as an AbstractBaseClass

----

As usual: a comparison with another language, in this case Java:

In Java one can declare a class as "abstract" in the language, which means that the compiler will not allow you to instantiate it. very useful. you can also declare classes final, which means that the compiler won't let you subclass them at all.

further you can declare abstract methods in abstract classes. an abstract method has no body, it is supposed to be declared in the subclass. this way a programmer can make sure that no one can instantiate his class without providing the body of the method himself, something that can be very useful.

ObjC lacks real abstract (and final) classes and methods, too bad. 

----

On the other hand, by correctly adding error messages to init functions, you can happily make a class run-time abstract.

----

Extreme ObjC newbie question:

I want to define an AbstractSuperClass that doesn't implement any methods.  At least that is what I think I want to do.  I want to have a class for a 'Function' that has methods like 'evaluate'. Then I want to create concrete Function's like sine, add, etc. that implement 'evaluate'.  Do I simply have a method inside Function that does nothing and then override it inside my concrete Function's?  Or?

-JustinGarcia

I expect you actually want a FormalProtocol. Try that. - Someone Anonymous

If you don't want a FormalProtocol, then you can either have implementations that do nothing, or you can call [self doesNotRecognizeSelector:_cmd] in order to make sure that the methods are implemented properly. See Apple's NSObject class reference page (on your hard drive or online) for more information about -doesNotRecognizeSelector: -- RobRix

