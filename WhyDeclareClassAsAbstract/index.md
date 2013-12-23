---
layout: page
title: WhyDeclareClassAsAbstract
---



Discussion moved from General/HowToDeclareAbstractClasses

Having very little experience with Java, I still fail to understand the importance of declaring an abstract class through the language. Is it simply to **communicate** that it is abstract to the user? If someone wants an abstract class to be instantiated, surely he must have a purpose for it. One doesn't use a class unless he knows what it does and has read at least some documentation on it. What advantages are there to declaring an abstract class in this manner rather than through the documentation/comments? -- General/RyanBates

The difference lies in the method of communicating that a class, method or whatever is abstract, final or whatever. I find that many think that there is no difference between runtime errors and compile time errors. I, for one, believe that the compiler is my friend and that it helps me write good code. The compiler helps me finds obvious semantic errors (as well as syntactic errors, but they are trivial in comparison) with typechecking, and access checking (public, private, protected). These make sure that I won't run in to runtime errors when I test the program. Many, however, seem to think that compile time checking is just overhead and that runtime errors and contracts by documentation is enough. I don't advocate completely static languages like C++, and actually like General/ObjectiveC more than Java, but I find that General/ObjectiveC lack some nice syntax that Java has, and which makes javac my friend, and gcc just another tool. When writing simple applications which can't run into many problems, scripting languages and dynamically typed languages are cool, but when writing larger apps, the lack of typechecking and access control can really drive me nuts. I program web applications in Java for a living, and what I really hate is JSP, since I get runtime errors, which could easily have been avoided whith some compile time checking - and no, precompiling the General/JSPs don't help, it's not syntactical problems. --General/TheoHultberg/Iconara

Don't strict protocols raise link-time errors if an allegedly conforming class doesn't implement them all? (It's been a while, I'm rusty on little details like this.) -- General/KritTer

*General/FormalProtocols do (which I assume you mean by 'strict' *Yep :)*. General/InformalProtocols don't. With General/FormalProtocols, I usually tell clients to just return nil or some other suitable value for stuff they're not using.*

Formal protocols do raise compile time errors, but that's another discussion (why General/FormalProtocols make General/AbstractClasses unecessary or not). -- General/TheoHultberg/Iconara

----

I understand the important difference between compile time errors and runtime errors; and I agree with you completely that compile time errors have a distinct advantage because they report the error immediately; however, is this the only reason for declaring a class as abstract? To report an error if one tries to instantiate it? My question is, "Why would one try to instantiate an abstract class in the first place?" I see it as a very hard mistake to consciously make. If you really want to avoid miscommunication about abstract classes, just put "Abstract" in the class's name.

My point is, one does not choose a class to do the job if he doesn't know how that class works or what it does. When was the last time you tried to instantiate an abstract class instead of a concrete one? I honestly couldn't see how anyone would make this mistake because if he doesn't know what it does, or how it works, he will not use it. Please do not take this as an argument, I simply want to understand the usefulness of declaring a class as abstract.

-- General/RyanBates

----

"My point is, one does not choose a class to do the job if he doesn't know how that class works or what it does." Maybe you think this way, but half the people I work with don't subscribe to this philosophy. The same people wouldn't be caught dead reading the documentation. If I don't make abstract classes explicitly abstract, they'll start creating instances.

This isn't such an issue in Objective-C because your abstract class constructor can just return a concrete subclass. Java can't do that, so it needs a mechanism to prevent creating instances of the abstract class.

The Objective-C world tends to have an enabling attitude. The Java world tends to have a directing attitude. See http://www.martinfowler.com/bliki/General/SoftwareDevelopmentAttitude.html

-- General/TerryWilcox

----

*The same people wouldn't be caught dead reading the documentation.*

Are you sure they don't read it in hiding? ;) Seriously though, I have a hard time imagining how this is possible. Do they simply try classes and methods out at random until they find out what works? Heck, how do they even know what methods a class can accept?

I still think placing "Abstract" in the name of the class is a better solution because it can be communicated before they even put it in the code and try to compile. But, I guess doing all that you can to communicate that it is abstract doesn't hurt anything.

I had read about the software development attitude some time ago and wasn't sure which side I leaned towards - guess I know now. :) I will just accept that as an answer to my confusion and move on.

-- General/RyanBates

----

*Never underestimate the burdens of authority. The only thing worse than being the boss, however, is **NOT** being the boss.*

By which I mean, at least the boss has the choice of when to direct, and when to enable. It's a hard choice to make sometimes.

----

You can't place *abstract* in the name of a method to show that it's an abstract method.

The discussion has come to focus on abstract classes, which isn't really interesting. In Java, if you declare a method abstract, the class containing the method must also be declared abstract. You rarely declare abstract classes that have no abstract methods (even though there are cases of this too, but they are not very common). So, no, to be able to declare a class as abstract is not such a big deal (even though I think that it's very good to have compile time checking of this). But to be able to declare abstract methods is much more important, for the reasons stated above (which assume that there is a reason for the class being abstract, and that reason is that there is one or more methods that are not/cannot be implemented).

I really can't understand that the     abstract keyword would not be useful. It's syntax sugar, but it's syntax sugar that speeds up the development process. Trust the compiler, don't do what it can do for you.

--General/TheoHultberg/Iconara

Here's my question again, though: what does it do for us that General/FormalProtocols and delegates don't? If there's no good answer to that question, then the answer to why     abstract is not useful is simple:     abstract supports paradigms that are redundant and should be avoided for "proper" General/ObjC ones. Delegation is more flexible than abstact subclassing, so why force General/ObjC to support the latter? -- General/KritTer (This *could* be put in W<nowiki/>hyNotDeclareClassAsAbstract, but please don't!)

I think this just comes down to the strong vs. weak type checking debate. People who like strongly typed languages, like Java, like them because of all the compile-time checking. Loosely typed languages, like General/ObjC, don't get so many compile-time checks. Adding "abstract" and the like, to increase compile-time checking, would make General/ObjC more strongly typed. That seems to defeat the purpose of using General/ObjC. There are a bunch of concepts in Java that I'd like to see migrate to General/ObjC (namespaces for one), but stronger type checking isn't one of them. I wish Java would loosen up a bit. -- General/TerryWilcox

----
*Here's my question again, though: what does it do for us that General/FormalProtocols and delegates don't?*

I don't think formal protocols and delegates can do the same thing. Formal protocols can't. Delegates do things differently. I would rather say Cocoa was designed in such a way that it does not have much need for     abstract (which is good, because it could not use it!). So, the design difference explains the syntax lack (or maybe it is the other way around, as General/ObjC came first).

Again, when do you need     abstract?
If you want to force a subclass to implement a certain method. That could just be a comment in the documentation and header. But it is easy to forget to add a method, and if you are just the subclasser and not familiar enough with the abstract class, you may get not-so-clear messages (though that should not be too difficult to debug). So to enforce it and help debugging, you write the method in the abstract superclass that calls an General/NSException with very explicit explanations of what went wrong. You cannot use protocols here. So here we are: using the keyword     abstract would help enforce the thing, without having to write cluttering code in the implementation for every abstract method. Are there examples of this situation? Not sure, but I sort of remember some examples in the Foundation and/or General/AppKit? Clearly not widespread, so to come back what I said first, I guess this is not a very Cocoa-ish design.

--General/CharlesParnot

Every class cluster, such as General/NSString, General/NSData, General/NSArray, etc. has an abstract class as a parent. They are written by having a small number of primitive methods which subclasses must override, and a large number of other methods which use the primitives. The abstract class implements the primitive methods to throw an exception, just as you say. There are a lot of examples in Cocoa, and I would say that this design is very Cocoa-ish, given its prevalence.

----

I will chime in with some information that might be relevant:  a common practice in the Smalltalk world is to make a method (called something like     subclassResponsibility: to which you pass the selector.  This method works almost exactly like     doesNotRecognizeSelector: but gives more specific information about what the developer needs to do.

Personally, I think that     abstract is kind of redundant when we already have a robust runtime environment to help us do things dynamically and it would only serve to make the language bigger.  Given a choice between simplicity and complexity, I would err on the side of simplicity any day.

--General/JeffDisher

----

*Here's my question again, though: what does it do for us that General/FormalProtocols and delegates don't?*

By inheriting an abstract class the subclass is of the same type as the abstract class, which is not the case of delegates.

*I would err on the side of simplicity any day*

If simplicity means leave it up to the runtime, I wouldn't. As I did rant about above, runtime errors make me mad, and why not let the compiler help me? Runtime errors can be very very very hard to find, especially in GUI-applications where you must test every possible combination of actions before you know that things work properly. There will always be semantical errors, but without compile time typchecking/anythingchecking you will have to deal with programmatical errors like using abstract classes when you shouldn't and calling the wrong method. There's a reason why static typing is so popular even when writing General/ObjectiveC (where all types are thrown out the door at runtime).

 --General/TheoHultberg/Iconara

----

*By inheriting an abstract class the subclass is of the same type as the abstract class, which is not the case of delegates.*

I know the definition. This doesn't tell me General/WhyDeclareClassAsAbstract.


* Class clusters are a good example. A superclass is used to hide the implementation details of its subclass(es).
* GUI building blocks are not. Flexibility is achieved by a superclass calling abstract methods at certain points. This is better implemented by calling a delegate's methods at certain points. This decouples the delegate from the GUI class, and gets us compile-time errors for free. We can use the same delegate to co-ordinate multiple GUI blocks, and use several delegates to control one block.


I appreciate that     abstract would help class clusters, but frankly, your unit tests should be trivially catching any missing methods. You can't unit test GUI blocks very easily, but subclassing is the wrong paradigm there anyway.

What uses of subclassing have I missed here?

-- General/KritTer

Why? Because an abstract class can define some methods in the class that call abstract methods and defer the implementation of specific to subclasses.  Or the abstract class can provide default behaviors for subclasses to use or override as desired.  From the larger perspective, the abstract keyword is just compiler syntax. In C++, a class is abstract if it all the methods are pure virtual, there's no keyword. -- anon

This is **how** it works, not **why** to use it. I already know how     abstract works, thanks. -- General/KritTer

----

I don't agree with the use-unit-testing-as-typechecker-mentality, I really like the power of static typing, combined with a dynamic runtime, and the thing I can't stand is that some simple errors are left to be discovered at runtime. I've said it a thousand times, *why not let the compiler do the job for you? why bother writing tests yourself that the compiler easily could have checked for you?*.

I do, however, agree with you that delegates are cleaner and more elegant in many cases, but not in all. You mention General/ClassClusters, I say General/TemplateMethod (and, no, it's not the same as delegation, although they are similar). That the abstract and concrete classes have the same type is also important. Extends can be very powerful (even if General/ExtendsIsEvil in general). 

Say I want to descripe news feeds. I want to have Atom-feeds and RSS-feeds. They both require some XML-parsing, but the format is different, so I encapsulate the public interface and the XML-stuff in an General/AbstractSuperclass and do the specific things in my subclasses. What have I gained over delegation? Well, the typechecker will agree with me that the subclasses are of the same type, and I can treat them as were they of the type of the superclass, nice. Could be done with General/FormalProtocols and delegation, true, but that would be a strange way of doing it. It's a trivial example, but I do think it is enought to justify why abstract classes are not meaningless even in General/ObjectiveC.

 --General/TheoHultberg/Iconara

----

*I don't agree with the use-unit-testing-as-typechecker-mentality, I really like the power of static typing, combined with a dynamic runtime, and the thing I can't stand is that some simple errors are left to be discovered at runtime. I've said it a thousand times, why not let the compiler do the job for you? why bother writing tests yourself that the compiler easily could have checked for you?. *

General/ObjC's runtime is dynamic because of the weak typing. It wouldn't make any sense to risk making it less dynamic simply because you want strong typing. If you feel that strongly about typing, you should be using a strongly typed language like Java or C++. They already have the strong typing you want. If you'd rather use screws than nails, you don't modify your hammer, you use a screwdriver. -- General/TerryWilcox

For those wondering about the use-unit-testing-as-typechecker-mentality, http://www.artima.com/weblogs/viewpost.jsp?thread=4639 and http://www.mindview.net/General/WebLog/log-0025 . It's interesting to watch the strong typing proponents re-evaluate their stand with the advancement of unit testing. -- General/TerryWilcox

I think you're wrong here. Static typing needs not interfere with a dynamic runtime, as we can see by looking at General/ObjC. Now, I read the two blog postings you mention, and I see their point. There is something they're missing though, they might have a solution, but they don't mention it: a typechecker can tell me on which line the problem lies and exactly the problem (i.e not the right type), a test can tell me in which method. Usually it's enough with the method, but hours can be spent when you're not sure. --General/TheoHultberg/Iconara 

Don't confuse strong/weak typing with static/dynamic typing, they are four different things and can be combined in different ways. Strong typing means that you can never, for example, treat a float as if it were a pointer. Static typing means all of your types are known at compile time. All C-based languages, including C++, are weakly typed; casting allows you to treat any byte in memory as anything you want. C is also statically typed, as there is no runtime determination of types going on. General/ObjC is weakly typed, since it's derived from C, and dynamically typed. A language like Lisp or Smalltalk is strongly typed but also dynamically typed. Something like ML (I think) is both statically and strongly typed. -- General/MikeAsh
