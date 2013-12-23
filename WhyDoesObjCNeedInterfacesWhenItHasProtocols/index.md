---
layout: page
title: WhyDoesObjCNeedInterfacesWhenItHasProtocols
---



You may have wondered why General/ObjC has interfaces as well as protocols. For instance, Java only has the equivalent of protocols and calls them interfaces. Well, actually this is quite an interesting question and one that you are well justified in asking.

Interfaces are different to protocols in certain respects. 

*Interfaces publish functionality and require that it is implemented. Protocols don't have to be implemented.
*Interfaces publish instance variables. Protocols don't.


This is all to do with the General/ObjC type system and the philosophy of General/ObjC. You may know that although in source code, you are encouraged to statically type objects where possible, the compiler and runtime throw all this useful information away and all objects whether return values, arguments or fields are of type id. 

Why do this? So that things are more dynamic. The object and its class are responsible for figuring out which method to call for a given selector. This allows such useful and powerful features as General/NSProxies and General/AspectCocoa. By making everything of type id, the runtime doesn't bother to check things are the right class and any class can be used. Errors occur at runtime, not compile time.

But if everything is type id and methods are decided upon at runtime, then why publish methods in an interface? Surely a better way would be to publish *all* methods in protocols and then have the *interface adhere to the protocol*. That way, if someone writes a new class that does exactly the same job, but isn't a subclass, they just mention the protocol and off they go. Their class can be used anywhere the original class was used, but they don't have to write out all those methods again in the interface, and are saved from interface debugging.

Also, some believe that public instance variables are a very bad idea. Lets say you have a public variable that is used in a lot of places, as an example say you have rectangular coordinates in x and y instance variables. Then someone comes along and says, hmmm, we need to do this in another way, actually the coordinates have to be polar. You have to rewrite all the code that relies on these variables being public. If the coordinates had been hidden behind access methods, that would have been no problem, we just apply a cosine to convert them. Only one class would have needed to change. Yet private instance variables may also be a bad idea. What possible convolutions of code are so crazy that even extending classes (subclasses) are not allowed to inherit those instance variables? This is probably why the default is protected.

Interfaces are then only for the benefit of subclasses.

So IMHO:
*all methods should be published in protocols (General/OnceAndOnlyOnce) 
*all instance variables should be protected and published in interfaces (General/OnceAndOnlyOnce)
*interfaces should not publish methods, but adhere to protocols.

I hope someone cares to disagree. -- General/MikeAmy

----

I'm curious, how often do you run into a "new class that does exactly the same job, but isn't a subclass"? I can understand this applying to a few of a class's methods, but certainly not every method of a class.

*Thanks for pointing that out, by "exactly the same job" I mean behaviour is unchanged for the subclass. I couldn't put it better than Liskov, so here's the link : http://www.c2.com/cgi/wiki?General/LiskovSubstitutionPrinciple -- General/MikeAmy*

And then what happens if you would like to add a method to one of the classes and not the other? I believe this adds some unwanted limitations.

Although I agree with protected instance variables, placing all methods in protocols is overkill. If you want to apply the General/OnceAndOnlyOnce rule, just apply some refactor by adding a protocol when you start seeing similarities in multiple interfaces - which will most likely only be a few of the methods in a class.

Well, you asked for a disagreement. :) -- General/RyanBates

----

It's a good point about refactoring only when you need to, but also you have to consider that the interface should be minimal, as explained in http://www.objectmentor.com/publications/isp.pdf. In that case, having methods in an interface which aren't disclosed in any protocol is questionable. You are tying code to that particular implementation/interface, aren't you?

Methods that appear in protocols are *guaranteed* to be General/OnceAndOnlyOnce. Interface methods are not so.

Please disagree some more. -- General/MikeAmy

----

Actually, I'm not very familiar with the Interface Segregation Principle. I need to read up on it more before I get back to you with an adequate argument; but, in order to keep this discussion going:

I consider a small interface as one where many of the underlying methods are private, but of course, this is not possible in General/ObjC. Wether the "interface" of a class is defined in a @protocol or @interface, it is still the interface. And placing the interface in a protocol does not minimize the interface of a class. Adding protocols is just a way of adding abstraction to a class's interface and goes against General/DoTheSimplestThingThatCouldPossiblyWork and General/YouArentGonnaNeedIt (unless of course, more than one class is using that protocol or you are creating a framework).

Let me know if I'm completely missing the point here. -- General/RyanBates

----

**Lost in a previous edit:**

Although I agree with General/MikeAmy in general, General/RyanBates has a valid point: we should not declare our methods in protocols by default, thete is little point in that, but we should use protocols more than concrete classes (interfaces in General/ObjectiveC lingo) in our class hierarchies. When writing small applications, controllers and views the protocol vs. interface is not really an issue. But when writing models, it could really make a differece, and it when doing that, that we should listen to what General/MikeAmy has to say. 

I very much recommend the article "Why extends is evil" ( http://www.javaworld.com/javaworld/jw-08-2003/jw-0801-toolbox.html ) from General/JavaWorld. Java was heavily inspired by General/ObjectiveC, most likely the class and interface distinction (as opposed to the C++ model with General/MultipleInheritance) came from, and there is a lot said about object-oriented ideas in the context of Java that we should listen to as General/ObjectiveC-programmers. 

The article talks, just like General/MikeAmy about how we should design class hierarchies with protocols and not with inheritance. It is very insightful. I also recommend the article "Why getter and setter methods are evil" by the same author ( http://www.javaworld.com/javaworld/jw-09-2003/jw-0905-toolbox.html ).

-- General/TheoHultberg/Iconara

----
Jumping into the discussion...

*I consider a small interface as one where many of the underlying methods are private, but of course, this is not possible in General/ObjC.*

You don't have to put all the methods of the implementation in the interface, so what exactly do you mean by that? The fact that you can still access them anyway at runtime, even from outside?

Also, my understanding of protocols is that they are here mostly for compilation check and is an addition to the static typing check done then, either to warn you that a method was not implemented when it should have (genuine protocols), or to prevent compilation warnings (informal protocols, but I don't think we are talking too much about these here). I think the use of protocols suggested here (minimizing interfaces) is not really their intended usage (so we should not even talk about it ;-).  -- General/CharlesParnot

----

Yes, that's what I was referring to. You can remove the method's interface from the header file, but it can still be accessed by any class - there's no private methods in General/ObjC. (And that's the way it should be IMHO.)

Also, although protocols may not be designed to replace interfaces, it's certainly worth discussing and I'm glad General/MikeAmy has brought up the topic. -- General/RyanBates

----

**And then what happens if you would like to add a method to one of the classes and not the other?**

Categories can add protocol conformities (eek, bad Engrish), can they not?

----

Hmmm, I think the point is that you don't have to list the methods in the interface twice, and General/CharlesParnot is right in saying that you don't have to list them anyway (I believe you just get a compile warning right?) but ... *protocols give you something to aim for, don't they?*. On the other hand, protocols can hold you back from innovations, but I believe that a protocols, exactly the same as classes, should have a single responsibility, so this also should not be a problem. The innovation goes in the new protocol. 

Anyway, by giving you something to *aim for*, while not tying you down, isn't this the intended idea behind interfaces in Java? In General/ObjC, interfaces are *tied* to an implementation, aren't they? True you have categories, but categories kind of preclude a complete different way of doing something. You're just adding a bit on. That's good for reuse, and I can see that usually there is one best way of doing things. So maybe interfaces are OK. Maybe we shouldn't want to do the same thing different ways, and this kind of refactoring is premature.

So then the key idea is what you are trying to achieve. Opening things up for extension? Making extension easier and more reliable? It just strikes me that we need interfaces, but we don't need them in more than one place. So more of the common bits should go in protocols. And yes I can see that refactoring should only be done as necessary.
I think you see protocols being used like this more in other languages like Java than in General/ObjC, but then it is true that General/ObjC has different motivations and wants to maintain C compatibility.
Hmmm -- General/MikeAmy

*In other words, you're just bitter because there's no General/NSCollection protocol?*

Probably I am bitter. But I should aim to be pragmatic. But then again, I've never needed it. Well, actually I did need it once, but it wasn't there, so I just wrote the exact same code 3 times.
But then, if General/ExtendsIsEvil as Holub makes a strong case for (thanks Theo), then perhaps there should be an General/NSCollection protocol and an General/NSCollection class with a uses relationship rather than an extends relationship to the other collections. I hadn't thought about that. -- General/MikeAmy

*Is that not what STL (and thus General/CocoaSTL) is about? i.e. it exposes the same interface for any "container" (be it std::vector, char*, int[], General/NSArray*, General/NSString* or whatever) without having any of the containers inherit/implement/extend General/NSCollection!?!*

Yes. -- General/MikeAmy

----


**all methods should be published in protocols (General/OnceAndOnlyOnce)*

How is that OAOO? A protocol is not an implementation. Are you referring to only the prototype of the method(s) (which would be OAOO)?

*Yep, thats what I meant, the prototypes. --General/MikeAmy*

I think you forget about the feature that I can implement only a subset of a protocol, e.g. I can add just a bare minimum of the General/NSTableViewDataSource protocol to my controller in order to have the table view show the data -- sure I won't get drag'n'drop, edit-in-place or similar, but that's not necessary for my first prototype...

*Perhaps that implies the protocol is not minimal?*


**all instance variables should be protected and published in interfaces (General/OnceAndOnlyOnce)*

As opposed to now where they are all sort of private (but perhaps only by convention)? at least I never ever write stuff like     object->someVariable. Or are you referring to KVC?

*Since reading* http://www.javaworld.com/javaworld/jw-08-2003/jw-0801-toolbox.html *yes my views have changed on this, perhaps protected isn't the way to go if you don't have any extension. Interesting article. I think there is a lot to learn from Java. -- General/MikeAmy*


**interfaces should not publish methods, but adhere to protocols.*

Why do you even consider looking at interfaces (i.e. the run-time type that an object has)? General/ObjectiveC is dynamically typed, so you should not query the class or expect a certain class. Ruby calls this General/DuckTyping, i.e. if it walks like a duck and quacks like a duck, then surely it must be a duck.

*Yes, that's right - somebody has to have the idea of what ducks walk like and quack like. That information is the duck protocol. Whether it's in your head or written down for all to see, it's still the duck protocol -- General/MikeAmy* 

As of such each method in General/ObjectiveC is a protocol of its own. So you can ask     respondsToSelector: and send the selector if it does -- why do you even need protocols? protocols seems to be a) for the compiler to perform the a compile-time check if you adhere to some contract and b) an easier way to check that a set of methods are supported.

But generally protocols are not very nice. E.g. take something like the to-many key-value-coding. You can implement insertion and removal of objects, and the run-time will see that you have no replace, but will simulate it from the little you have -- if you need the performance, you can also implement the replace method, and it'll be used when appropriate. With (formal) protocols it's really all or nothing.

*I wasn't aware the runtime was capable of this. Are you sure that's what you meant? Or have I missed something?*

**Sorry, this is not the General/ObjectiveCee run-time, but the Foundation run-time, i.e. this functionality is provided by another class which acts as mediator for the object which lack replace semantics.**

----

One of the things I like about Cocoa over Java is that Cocoa does not overuse formal protocols (and Java interfaces behave like formal protocols).  Because of the interface all-or-nothing (you *must* implement all of these methods), you end implementing a lot of do-nothing stub methods, or you extend some class that is nothing but do-nothing stub methods (the swing event handler classes come to mind here), which seems like a lot of busy-work.   There's probably some middle ground between Java's inistance of implenting all of an interface and General/ObjectiveC's rather freewheeling spirit.  Things like the Cocoa tableview data source is one of my ideals in this world.  It's an informal protocol (meaning that anything can become a datasource without having to explicitly declare support), and the code that uses the protocol can do run-time error checking ("thou shalt implement these two methods")  Something that actaully do require everything in the protocol to be implemented (like General/NSCoding), then formal protocols are a great tool.

----

Hmmm thats really interesting - *As of such each method in General/ObjectiveC is a protocol of its own.*. One method in a protocol? Usually I end up with 2 or 3. But 1? You'd check every time that the object responds to that selector. You'd always make sure.

*Yes, you'd need to perform the check each time, if it is okay that the "user" passes an object which doesn't respond to the method. Otherwise, just send the message and get a run-time exception, or check it when the user hands over the object/delegate (since clearly the user was asked to provide an object implementing some method, which he did not do) -- General/NSResponder provides a convenient tryToPerformSelector:withObject: which I wish was part of General/NSObject.*

But if you group related methods in a protocol, doesn't that make sense?

*Make sense how? I assume we are dealing with formal protocols -- take a look at the description of mutableArrayValueForKey: in the General/NSKeyValueCoding protocol. This returns a mutable array for the given key, but it does not require that the underlying object adheres to a given formal protocol, it is quite flexible, and that benefits the implementor of the underlying object (for which a mutable array must be simulated).*

*The same is the case with the General/NSTableViewDataSource protocol, here the implementor of this (informal) protocol can implement at least 3 depths of conformance to that protocol, again to the benefit of the application writer, unlike e.g. Java's interfaces, where you often need to implement stuff that no-one uses. The same is actually the case with General/AppKit's General/NSTextInput protocol, which is a formal protocol and contain 12 methods. So in order to support text input I need to implement 12 methods, half of them irrelevant to my application and never sent by the system.*

Is perhaps thinking 1 method in a protocol the opposite extreme to using huge interfaces. I think there is some value in that though. Recommend this discussion at http://www.c2.com/cgi/wiki?General/ThereAreNoTypes. Yes that is indeed a very valid point you've made. 

So yes I agree with the points about protocols being a compile time check and an easy way to check that a set of methods are supported. But *generally protocols are not very nice*? That seems a bit shaky and flys in the face of OO design a bit, methinks. But I could see how *unnecessarily bloated* protocols could lead one to that conclusion. -- General/MikeAmy

*I should have stressed that I am talking only about formal protocols.*

Ok. That makes more sense. So would you agree that a really loose coupling to protocols is a good thing and is acheived by this? It leads me that way. -- Mike

----

Does anyone have any hands on experience with this? Sometimes it's hard to properly evaluate a theory until you put it into practice. I'm interested if it was found to be practical or impractical. -- General/RyanBates

With defining all method interfaces through protocols instead of the class's interface. (applying the rules that General/MikeAmy listed above):
*all methods should be published in protocols (General/OnceAndOnlyOnce)
*all instance variables should be protected and published in interfaces (General/OnceAndOnlyOnce)
*interfaces should not publish methods, but adhere to protocols.

----

Well, I tried to make a list of what to compare (in no particular order):
Methods could be published:
*only in interfaces, like ...?
*Some in protocols, but mainly in interfaces which sometimes conform to protocols, like in Cocoa
*All in formal protocols, interfaces only define variables, like what was originally suggested.
*All in informal protocols, interfaces only define variables, check that the object responds to the selector each time.
*Only in implementations (*not published?*)
What have I missed? 

What does GCC and the language think about all this? When do you get warnings?

*You get warnings when sending a message to a class for which the compiler has not seen the method defined, either on the class, category of the class or the same for any of the super classes. The exception is with     id, which never gives a warning.*

**GCC also checks in any protocols qualifying the object**

**This check works 99% of the time, but it is an heuristic, i.e. we can overload     performSelector: or have the category loaded at runtime and thus defined elsewhere (although we would then probably have a header defining it)*

Also I think I take issue with myself now over whether variables should ever be exposed even to subclasses, or even whether you should ever extend at all.

-- General/MikeAmy
----

Trying to only use protocols, you have to move whatever inheritance hierarchy you have from the interface level to the protocol level:
    
// .h
@protocol Thing <General/NSObject> 
- (void) doThis;
- (id) doThat;
@end
.
@interface General/MyThing <Thing> @end
.
// .m
@implementation General/MyThing
- (void) doThis { General/NSLog(@"Did this"); }
- (void) doThat { General/NSLog(@"Did that"); }
@end
.
<Thing> wotsit = General/[[MyThing alloc] init];
[wotsit doThis]; 
[wotsit doThat];
[wotsit class]; // requires <General/NSObject> protocol to be inherited by <Thing> protocol
.

If <General/NSObject> isn't there then compiler won't know what else wotsit can do as there is no class information.
Also implementations can't have protocol reference lists and rely on interfaces so one simply *cannot* do away with interfaces.
Better, worse, same? -- General/MikeAmy

----

So one may ask, why are we using OO? Encapsulation does AFAIK predate OO and is simply known as data abstraction. Likewise, protocols are what General/FunctionOriented languages call signatures.  And these actually use type-inference to check types at compile time. E.g. if you have:
    
fun is_equal a b = a == b;

Calling     is_equal("hello", 5) gives a compile error (contrary to dynamic typing, where it gives a run-time error).

It gets more advanced, like:
    
fun f a = 4*a;

Now it knows that     f takes and returns an integer. So if we do     is_equal(p, f(q)) it knows that both     p and     q must be integers.

One reason I see why having static typing (with type-inference) is not possible is that not all code is seen at compile time. I.e. the OS will return values which have unknown type at compile-time.

*True, but even functional languages has classes and objects in either Monads (eg Haskell) or Worlds (eg Clean). The problem solved by OO there is state. Anyway, OO is kind of an assumption for our argument. -- General/MikeAmy*

----

Did you know you can stick the instance variables in the @implementation section? This means that you can have an entirely protocol-based structure, with @interface "stubs". This would allows General/MultipleInheritance without many of the usual problems. The only problem with this is that you have to implement everything all over again when you want to "sub-protocol" an existing protocol. Another problem: Interface Builder wouldn't work at all, at least not without a major overhaul. But it always seemed weird to me that variables showed up in the "interface" of an object, when General/DirectAccess has always been evil. --General/JediKnil

<response type="sarcastic"> Oh right. How are we supposed to create instances of protocols, when they have no     alloc/init? They have no structure at all! You can't enforce an object hierarchy with protocols either. How about we seperate variables from the methods? How about we put the variables in some sort of     structure, and not have the methods tied to anything! Maybe we should change the message format from [target doThisWithThis:(id)obj] to [tell:(id)target toDoThisWithThis:(id)obj]. If we're just going to call a group of methods groups (a protocol is basically a group of methods) a class, why not call it a library? Hey! I've got an idea! Why don't we all go back to General/ProceduralC?

Basically, there's no difference (between an ideal interface and an ideal protocol). But a protocol shouldn't be used as a class. You don't receive the main benefit of subclassing if you do: inheriting the superclass's methods (since in this case you would have to rewrite them yourself). I prefer General/AbstractSuperClass**'es, which allow you to declare what methods a subclass *must* implement, while providing a base implementation. (Switch to Java! And Cocoa is *not* a language!) The whole point of OO is to associate the methods *with* the data. </response> --General/JediKnil

At one point it may have been, but KVC and accessor methods have changed that to a certain extent. The public state of an object may have little relation to its internal state. Count me in the all-ivars-should-be-private camp. --General/DavidSmith
