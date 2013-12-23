---
layout: page
title: ExtendsIsEvil
---



Extending a class may be evil because the subclass is the coupled to the inherited class.

See: http://www.javaworld.com/javaworld/jw-08-2003/jw-0801-toolbox.html

Synopsis:

This is a Java article which may or may not apply to ObjC. Allen Holub goes over why designers should design models in terms of common interfaces (protocols in ObjC) rather than extension relationships, that is, rather than extend a class, which ties you to the class you are extending, have a common interface and delegate to the class you would have extended. The two classes are then more loosely coupled. But there are some caveats, so I suggest you read the article. It may change your views a bit. But keep in mind that maybe ExtendsIsGood.

Objective-C gets around a lot of the 'extends is evil' by categories and informal protocols.  We don't have to use inheritance to add features to class or to come up with flexible, decoupled architectures.

*I've heard it expressed that inheritance can be viewed as just being a hack to get some implementation for free... and when you think of it that way, protocols seem a great way to publish your interface.*

----

**Statement:** Inheriting a class's implementation is a bad thing because the subclass is too closely coupled to the superclass, and therefore decreases the flexibility of the entire program. Instead, inherit only the interface.

**Argument:** Inheriting a class's implementation is just as flexible as inheriting its interface because the subclass always has the option to override the superclass's implementation.

----

**Statement:** If the superclass changes its implementation, you may mistakenly break the functionality of a subclass.

**Argument:** You are just as likely to break a subclass as you are any other class which uses that method. A class that delegates the work over to another class is just as fragile as the subclass. This is assuming the subclass supports all functionality provided by the superclass.

What about @protected methods and instance variables? The subclass could have coupled to them but a delegating class cannot. (NB protection only applies to Ivars in ObjC)

*You're right, this argument doesn't apply for protected methods and instance variables - only to private and public.*

----

**Statement:** If a superclass adds a functionality/method, the subclasses may not support it.

**Argument:** Look at the subclasses and see if they support it. Most likely they will and you will get the additional functionality in the subclasses for free. If they do not support it, create a new subclass and add the extra functionality there instead of the base class.

**Counter-argument:** If a superclass declares a new method, a subclass will automatically inherit it, without notice. Now the implementation of the subclass may break, depending on how closely coupled it is to the superclass. If the method is instead declared in a formal protocol, you'll get a compile time error, and thus a notice that something have happened.

**Counter-counter-argument:** Yes, but a simple text search can find all subclasses of that class - which is basically what the compiler would be doing. It may be a convenience, but the price of a more complicated design seems too high for this convenience. Again, this argument doesn't apply for the creation of frameworks where you cannot alter the subclasses.

----

Basically, every subclass should support **ALL** the functionality of its superclass. This was the key problem of the fragile classes in the article's examples. Inheriting only the interface is not a direct solution for this problem - it is simply a way to make the problem more clear. The price of abstraction is a more complicated design - which is why I prefer to only add abstraction when I know it will be necessary. Please feel free to add your comments to the above arguments. -- RyanBates

----

It's probably very difficult to make general statements about how to make OO design. Take something like AppKit, should not all controls inherit from NSControl? Should not all views inherit from NSView? Or should that have been protocols only? That would make it quite a lot of work to introduce new custom views.

Different rules apply in different problem domains -- personally I use close to no inheritance in my work, but that is because I work with more scientific programming, i.e. algorithm implementations and such -- I still use the data abstraction (i.e. encapsulation) that OO gives me, and rarely I do inherit from some base class, though mostly I use base classes to define protocols.  I program in C++ mostly, where an abstract base class works just like a protocol because of multiple inheritance -- it even has the added benefit that one can actually inherit part of an implementation. E.g. we may make an protocol like this:
    
@protocol
- (BOOL)equalTo:(id)rhs;
- (BOOL)notEqualTo:(id)rhs;
- (BOOL)lessThan:(id)rhs;
- (BOOL)greaterThan:(id)rhs;
@end

Where only one method needs to be abstract, since the rest can be implemented from that single method.

    
@protocol
- (int)compare:(id)rhs;
@end


----

I'd like to point out again that the article was written for Java, and I think Java interfaces don't correspond to ObjC interfaces but protocols, so that may confuse some and it may be helpful to clarify exactly what is being talked about in the arguments above. There is also the Liskov substitution principle - "The behaviour of a program should be unchanged when a subclass is substituted for its superclass." 
Oh and it's very good to be argumentative and pick articles apart. Critics are your friends.
-- MikeAmy

----

I think (after reading this and the link) that the implications are meant for any Objective Language including ObjC, simply because the concepts are Abstract enough to apply.  I do not agree that inheritence is evil in Obj C.  
the concept of inheritence brings developers more time to devote to the code that is Specific to our apps, in other words, we get more time to spend on the things that are important to us.  
But there are cases where, inheritance can be trouble, such as the instance where the class we are subclassing doesn't support the methods of its superclass properly, and even then I would'nt call inheritence evil, but this particular application of the concept.
Inheritence is a tool, like fire.  And like fire, it can help you, or it can hurt you.
-ted

----

Ted speaks wise words. Extends is evil, but not in the way that is should be banished. It could be conidered a bad smell (like BadSmellsInObjC), when classes are too tightly coupled (which they often become when they form a extends-hierarchy). Extends can (and should) be used properly, but is often overused. A good example of this is an anecdote in the foreword of AaronHillegass' BookCocoaProgMacOSX where Aaron explains why he thinks that Cocoa is superior to other frameworks, which is largely because of the use of delegation before inheritance.

What ExtendsIsEvil wants to teach us is that it often suffices to use delegation instead of extends. Extends will complicate your life, whereas delegation will simplify it. 

There are of course examples of good uses of extends, there is a reason why it exists at all. One example is the TemplateMethod DesignPattern, and also other uses of abstract classes.

-- TheoHultberg/Iconara

