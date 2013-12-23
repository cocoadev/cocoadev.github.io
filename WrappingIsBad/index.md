---
layout: page
title: WrappingIsBad
---

 

"Wrapping" is when one class makes another class its ivar and passes messages sent to it directly onto the ivar, adding nothing but call overhead.  Example follows:

    
@interface General/MyWrappingClass : General/MyWrappingClassSuperClass {
    General/MyWrappedClass *_poorMisfortunateWrappedClass
}

- (void)someMethod;
- (void)someOtherMethod:(id)someArgumentsMaybe

// here comes the wrapping
- (void)wrappedMethod;
- (void)wrappedMethod:(id)withArguments;
@end

@implementation General/MyWrappingClass
// lots of code 
- (void)someMethod
{
   // do some reall processing
}

- (void)someOtherMethod:(id)someArgumentsMaybe
{
  // do some more processing
}

// here comes the wrapping
- (void)wrappedMethod
{
  // do nothing but pass along the message
  [_poorMisfortunateWrappedClass wrappedMethod];
}

- (void)wrappedMethod:(id)withArguments
{
   // same as above
  [_poorMisfortunateWrappedClass wrappedMethod:withArguments];
}



**Why is the above not cool?**


*Because it duplicates an interface.
*Because it adds useless call overhead.
*Because it should be handled with inheritance.
*Because it creates a mess out of your class hierarchy.


----

*The next two entries are counterpoints from later in the discussion that summarize the other side nicely:*

This notion of "Wrapping" is also called Object Composition. Object Composition is a very good design pattern that prevents certain problems. Like all design patterns it can be abused. But the notion that Object Composition is "bad" is ignorant of software design. 

To elaborate on why Object Composition can be good: 


*it allows you to rescope or redefine an interface. Why subclass a dictionary and its huge interface if you only need and/or can rely on part of it
*it allows you to defend your object's implementation and treat composed objects as an implementation detail. This is called encapsulation.
*it aides in delegating behavior to other objects. A'la delegation.
*it avoids cluttering your object inheritance map with lots of questionable subclasses. big inheritance maps are the bane of C++ and other OO languages. Solutions like multiple-inheritance can be mis-used to actually make this worse.


-- General/MikeTrent

I also need wrapping in some of my applications to allow stuff such as

*have multiple instances share the same data (General/LazyPattern?)
*have an object change behavior (General/StrategyPattern)
*restrict the interface of the actual object (e.g. turn an array into a stack)
*add code to be executed before/after the real method
*...


----

I do not agree. Wrapping, or delegation, can be used for many things, for example to avoid meaningless inheritance or multiple inheritance (remember General/ExtendsIsEvil). It can be used with interfaces (a.k.a protocols) instead of inheritance to avoid close coupling of classes in a hierarchy ("extend by delegation"). The example above is not an example of good delegation, of course, but there are many (I really recommend reading the General/ExtendsIsEvil article), including the General/ProxyDesignPattern (http://c2.com/cgi-bin/wiki?General/ProxyPattern).

--General/TheoHultberg/Iconara

I also disagree... It may be that you want a class to present a certain interface, and that the implementation you want is already in another class... Just because you are wrapping it right now, doesn't mean that you won't change to a different implementation later.  Wrapping can be good, because it can be a far more elegant solution that General/MultipleInheritance.  When I took General/ComputerScience in University and were studying different ways of constructing software in General/CPlusPlus, far too often people inherited from a pile of classes to create their final class with the interface they wanted to have.  The much more elegant solution is to wrap the class into a has-a/uses-a relationship rather than is-a relationships.  The one thing that one needs to be careful of in this is not carefully considering the interface you present of the wrapped object.  In the context of the wrapper object, there is a good chance that a particular subset, or combination of operations will be performed on that object... rather than export that code to users of the wrapper, it belongs in the wrapper.  It feels like a similar idea to General/GettersAndSettersAreEvil.  Make use of has-a relationships in preference to is-a (inheritance), but put the logic for using those ivars in the class that has owns them, not the classes that use it.  Oh, and complaining about call overhead is silly... General/DennisRitchie told everyone that function call overhead in C was next to minimal, so everyone started making really beautiful software... when they found out he lied to them, and there was overhead induced, "it was too late, they were hooked" and everyone was using it... Function call overhead is negligible in the face of modern compilers, computers, and should not be used to discourage people from making modular code.  I don't think any piece of code I have worked on recently needed to worry about being a couple stack frames higher (but then I don't do much recursion... serious recursion is one place to watch out for this, maybe...)...

----
I'll also voice my dissent to the original statement. 

'Useless call overhead' is a straw man. Method calls are cheap. Yes, implementing a lot of message forwarding can be laborious. Yes, some languages give 'cooler' ways to forward messages that General/ObjectiveCee does not provide. That does not make forwarding messages a bad thing.

'Because it should be handled with inheritance' - I have the same arguments as those listed above. Class re-useability is higher when inheritance is minimized. Yes, inheritance

'Because it creates a mess out of your class heirarchy'. Define 'mess'. Lots of classes with shallow inheritance heirarchies vs lots of classes with a deep inheritance heirarchy? Why is this bad? Since General/ObjectiveCee does not have multiple inheritance,  you really cannot benefit from code reuse WITHOUT wrappering instead of using inheritence. And if you have deep inheritence hierarchies, you run the risk of having to write a lot of special-case code in your message-forwarding scheme - because you're trying to reuse *complicated* behavior instead of simple behavior.

--General/TimHart

----
I guess my argument isn't very popular.  Let me try and defend it.

� Call Overhead:  This depends on what kinds of apps you write.  If you're writing code that gets called over and over again, if you're writing apps that try and do as much as possible in a given time frame (e.g. real time apps) then you do care about call overhead.  C function calls, are pretty cheap, Objective-C method calls are substantially slower; so much so that people often try and cache some IMP pointers to reclaim some of that overhead, and General/NSProxy calls pose, for many cases, an unacceptable cost.  I don't think it's a straw man, and limiting stack frame popping and pussing, in addition to limiting what the General/ObjC runtime has to do before any stack operations take place is an important optimization technique.  Apple provides tools that warn you when you've gone too far in the stack before doing anything meaningful.

� Keeping the hierachy clean:  Yes, I think a square hierarchy is usually better than a rectangular one; disproportionate depth or width of the hierarchy creates either too many specialized classes whose functionality could be unified saving on code (intialization and protocol implementation, for instance) and speed (fewer init methods to run, less stack operations) and at the same time not necessarily incurring when only a subset of a class' functionality is used.  On the other hand, when the class hierarchy is too deep it could be a sign that you have too many "pseudo abstract classes" or classes that never get instantiated.  I do not agree with the specialization argument.  If a subclass provides functionality you know you don't need, don't instantiate it.  No one ever said you can only use the bottom most classes on the class heirarchy.

� Duplicate interfaces.  These for all the reaons that duplication of any data is bad.  It adds a maintainability problem, where you have to keep the interfaces synced.  It also, as the name suggests, is duplication -- or in other terms waste.  And just as importantly it confuses the interface users.  Polymorphism is one of the stapples of OO programming, and it is an extraordinarily good thing�.  However, duplicate interfaces undermine the very essence of what makes polymorphism so great, and they add a level of static behavior that is undesirable and is exactly what General/ObjC tried to free its users from.

----

I disagree too. Wrapping can be a very powerful design tool if used correctly. On the other hand, used incorrectly and you can see the side effects mentioned above (messy hierarchy, useless call overhead, duplicate interface, etc.)

First of all, have you ever experienced a problem with call overhead? It sounds like it would be a problem in theory, but in practicality, there are many other areas which which slow down the app. Do not let optimization worries get in the way of good design. Just General/OptimizeLater. (Note, there are some exceptions to this rule, as with almost any "rule").

One good reason for wrapping is that it adds flexibility. The whole point is that you do **not** directly duplicate the interface when wrapping a class. Usually you change the interface to make it fit well in your design. Or you may be wrapping a class to hide the class which is really doing the work. This allows you to change the wrapped class without changing your entire code - you only need to change the wrapper class.

For example, you might wrap an external socket class or database accessor class. This adds great flexibility because you can substitute a different socket or database classes without breaking all of your code.

Wrapping itself is neither good nor bad, it completely depends on where you use it. Remember, there is a time for everything: a time to delegate, and a time to subclass; a time to optimize, and a time to design; a time to use accessors, and a time to not use accessors; etc...

-- General/RyanBates

----
As the OP, I disagree all of you!  I don't agree with General/RyanBates' example of flexibility; e.g. you want your code to work with some db, but you might deploy on multiple platforms and load some bundle dynamically which the wrapper then uses as it's ivar.  That's cool.  BUT, if that's the case, why not make the bundles adhere to a protocol!?  I don't get why you'd use a wrapper.  If your implementation changes, no problemo, it changes, but the interface can't change or you break your code.  WHERE that interface change takes place doesn't matter -- that's just postponing the inevitable.  If you're wrapping b/c something did break and you don't want to fix your code, then you start growing code in an ad hock (or odd hack) way, that gets very patchy... but if you're saying Wrapping is cool for a quick fix and acknowledge that it's a duct tape solution, then I agree it's good to get something back up prompto.

----

Real time apps have always been considered a special case of software design in that the developer must balance keeping their code maintainable and making it performant. That argument alone doesn't validate the universal statement that wrapping is evil. Several possibilities for optimizing wrappered methods exist that still allow a developer to delegate ( wrapper ), and still get performant code. These include using a static method variable to cache the IMP pointer, using Objective-C++ and inline methods to avoid expensive messaging and stack pushing/popping. You can also define macros to inline the code while still keeping the 'source' in one place. I will agree that choosing not to wrapper is **a valid ** optimization choice. One of several. Note that optimization choices and design choices are two completely different parts of programming. They are often in tension to some degree.

Errmmm... Both Foundation and General/AppKit are much wider than they are deep. They would not be considered 'clean' based on your definition. A good percentage of Object-Oriented design literature discusses the benefits of minimizing class coupling as much as practical. Deep class heirarchies suffer from maintenance issues when a class higher up in the chain is modified. A common rule of thumb is that if a class heirarchy is more than 3 or 4 levels deep, it needs a second look to confirm that such a heirarchy is necessary. Of course, the side effect of such a rule of thumb is that most class libraries are much broader than they are deep.

I will agree that all the mind numbing message-forwarding ( what you call duplication of interface ) should be considered when deciding whether or not wrappering is an appropriate choice. Both in terms of current work, and the potential effect that maintenance will have. I do not think that such duplication of interface alone is always and absolutely a bad thing.

From the Portland Pattern repository:
http://c2.com/cgi/wiki?General/DelegationPattern - delegation is the more common term for wrappering. As mentioned on that page, it's the basis for many other design patterns - so much so that calling it evil is really invalid.

It seems that it's been in vogue lately to proclaim a bunch of 'General/IsEvil' doctrines. Frankly I'm getting tired of it. All software design is about trade offs and choices. Any design pattern can be misused. I do think that it's important to communicate HOW a paradigm can be misused, but to call it evil implies that it shouldn't be used at all. Just like inheritence, overloading, overriding, class methods, and any other language feature, learn the tools, learn their good points, and learn their bad points. 

-- General/TimHart

*This is why I prefer 'Such and Such Considered Harmful.' I've never used goto because it would be in my case a nightmare to maintain, but I've seen points made regarding situations where it can be the cleanest way to reuse code.*

----

Interestingly, the General/LawOfDemeter suggests that you should wrap interfaces. To illustrate, I may have a class whose instances manage an array of items, and may want to expose some interface regarding them. Should I have a method returning the General/NSMutableArray ivar, or should I have methods such as     -addDingus: and     -removeDingus: and the like?

I don't necessarily adhere to the General/LawOfDemeter in all situations, but it's an interesting thought. If you have a method returning the array, it's an even trade; you don't have all these methods to maintain, but you limit your implementation by the interface of a second class.

-- General/RobRix

----
Hear ye, hear ye, this is the OP.  First off, wrapping isn't evil, it's just not good, and I honestly can't think of a time when a protocol or inheritence wouldn't do the trick in a much cleaner way; but those arguments have already been argued for (by me) and against (by everyone else :( ).

However, I will disagree categorically with one statement made on this page: namely, that wrapping and delegation are one and the same.  They are not.  Wrapping is when the ivar can not be set externally, it is initialized by the class that wraps it and it is deallocated (I hope!) by the class thata wraps it.  Delegation is when a class needs to consult or inform another random instance of a random class.  The General/AppKit classes make fantastic use of delegation; I love the concept, and I love the simplicity of the implementation and the incredible extendability it allows.

----
Example of  wrappering that inheritence or protocols would not satisfy...

    

@interface Node : General/NSObject

- setName:( General/NSString* )name;
@end

//imagine obvious implementation...

@interface Graphic : General/NSObject

- setX:( int ) x;
- setLabel:( General/NSString* ) label;

@end

//imagine obvious implementation...

@interface General/NodeGraphic :
//three design choices here - inherit from either node or graphic and wrap the other,
 //or wrapper both and inherit from General/NSObject
- setName:( General/NSString* )name;
- setX:( int ) x;
- setLabel:( General/NSString* ) label;
@end



Even if you declare a protocol for Node or Graphic, you still have to wrapper an instance of Node or Graphic if you're going to gain any reuse from your code.

I've never seen any distinction between wrappering and delegation as you've described. Objective-C documentation does use the term delegate and delegation when submitting your own delegate - to be sure, but OO literature at large - and the General/GangOfFour book specifically doesn't make any such distinction. In fact, in the Delegation section in the introduction, it specifically mentions delegation with regard to design patterns that both allow the developer to assign the delegate, and not assign the delegate.

Another interesting note: The General/GangOfFour's 'Adapter' pattern lists 'Wrapper' under 'Also Known As'. In this case, it's defined as 'Convert the interface of a class into another interface...' 
-- General/TimHart

----

I do see people making pointless wrappers (which probably do more harm than good), but as with everything, everybody misuses it.

If you introduce a wrapper, you probably have a reason to do so. Perhaps the OP could point to some real-world examples where something done as a wrapper could have been done more elegantly w/o it being a wrapper?

I am reminded of the previous General/MultipleInheritance discussion where someone wanted General/NSButton to inherit from both General/NSCell and General/NSView... ;)

Btw: Automatic General/KeyValueNotifications are sent by swapping the isa-pointer of the object. So effectively it wraps the entire object! It would actually be very useful if such feature was available for mere mortals to use, e.g. in order to "wrap" the objects contained in an array or similar, i.e. a way to change behavior of an object w/o changing the source, and affecting objects that you did not instantiate yourself (and further, affect everybody holding a pointer to that object, contrary to just storing a wrapper in the array).

Btw2: Look toward C++ for a lot of ingenious use of wrappers, iterator wrappers, container wrappers (views), smart pointers, various forms of the pimpl pattern (pointer to implementation).  Wrappers can be extremely useful!

----
Yes, I do think that General/NSButton should have used MI if it were available, and that wrapping is a solution to circumvent MI's absence from General/ObjC.  I think I come from a more spartan tradition of coding, and many of the advantages of wrapping sited on this page make me uneasy -- e.g. storing the same value in different locations.  Again, duplication of anything (data, interfaces, code, etc) is not good.  But I also realize that computers have changed a lot since the Apple ][c and it maybe time for me to learn to use some of that extra elbow room.  I will soften my stance and say that wrapping is OK iff 


*it's not wrapping -- some processing is done by the wrapping class
*or if more than two instances are being wrapped.  MI with more than two super classes gets more awkward, imho, and introduces a host of problems usually pertaining to confliciting implementations of the same method.


----

Today I noticed a case where I wanted better wrappering from the Cocoa frameworks... Why can't I ask for a substringWithRange: (no not royaleWithCheese: ) from an General/NSAttributedString or it's subclasses like General/NSTextStorage...?  Technically an General/NSAttributedString is not an General/NSString... it just has one... The primary function of an General/NSAttributedString is actually a list of attribute ranges, and thus seems kind of backwards... Either the General/NSAttributedString should show a string interface, or like a delegate an General/NSString should be able to attach an attribute range list...  

----

Instead of delegating, why not have General/NSAttributedString be a subclass of General/NSString?  This is obviously beyond *our* control, but the question is about the philosophy.

----

*Yes, I do think that General/NSButton should have used MI if it were available*

What about controls which may use more than one cell? or may control several values shown using the same cell? If you say that's okay to wrap here, what about the chance of expanding General/NSButton down the road to actually do hold two cells (like image cell (icon/disclosure triangle/you name it) plus text cell, then would it not be a problem if we'd used MI in our original implementation?

*wrapping is OK iff [...] some processing is done by the wrapping class or if more than two instances are being wrapped*

So since I have my General/NSDocument subclass forward methods such as save:, saveAs: etc. to the actual document (model) instance that it "wraps", this design is not okay?

Let me repeat my question above: Do you have any real-life examples where wrapping was mis-used? It's a no-brainer that re-implementing *all* methods of the class being wrapped w/o adding logic makes no sense, but that could be applied to everything, e.g. "functions are bad when they call one other function with the same argument...", "inheritance is bad when the subclass doesn't introduce new methods...", "programs are bad when they just return 0 and do no processing..." etc.

----
Yes, all those things are equally bad in my opinion, with the exception of subclassing and introducing no new methods; even if all you do is override that's cool.  As for your General/NSDocument, it sounds like you might have a controller/model melt a bit.  The model should do no more than return some serialized data that the General/NSDocument subclass then deals with (e.g. writes to disk, sets up file packages, etc).

This page was started as the direct result of the MI discussion.  As for controls that use more than one cell, in those cases, as I mentioned earlier, wrapping two or more instances is almost always cleaner than using MI, or any other design paradigm.  Same things applies to objects that essentially behave as structs.

----

*it sounds like you might have a controller/model melt a bit*

Well, I didn't give a true picture of my design -- the actual model doesn't handle the save UI, but neither does the General/NSDocument instance, it calls another object to do it, and so its action methods for saving documents do wrap for another object.

The reason for this is that I have one General/NSDocument subclass that handle a single document, and one which handle an array of documents (i.e. a project).

So the easy way to get maximum code re-use was to wrap the model in my own document class, have the General/NSDocument subclass which handle a single document wrap for this class, and have the General/NSDocument subclass which handle the array of documents, wrap for the "current document" (set using an ivar).

It might sound a bit confusing because of using the word 'document' for different things.

Here's a conceptual description using other words:

    
// has save:, saveAs: etc.
@interface Document
{
    Model* theActualData;
}
@end

// forwards to theDocument
@interface General/SingleDocumentController
{
    Document* theDocument;
}
@end

// forwards to currentDocument
@interface General/MultiDocumentController
{
    General/NSArray* allDocuments;
    Document* currentDocument;
}
@end


As the OP I've significantly changed my view of wrapping as the result of this discussion.  I'm up against the following problem, though: I can either wrap an object that the wrapper relies on to do a lot of its processing, or I can have other objects have ivars that track the would-be-wrapped object.  Both solutions seems wasteful.  One obvious way out is to merge the two classes or make the second class a category of the the first, since it is instantiated only once per instance of the first class, but that kind of feels as wrong as mereging the functionality of an General/NSUndoManager with General/NSDocument -- that's probably not the best example, since instantiation of those two are in no way linked.  Any ideas?

----

*As the OP I've significantly changed my view of wrapping as the result of this discussion*.

This makes me very glad. Not that you have changed your view to something closer to my own, but that, as a result of this discussion, you have learnt something (and a few others have learnt something too, I guess), and that's what this site is all about. When I posted the first counter-post, I thought that, well, this is another of those design discussions that can go on forever, just repeating the same arguments over and over. Now it turns out that it wasn't. Nice. Made my day.

--General/TheoHultberg/Iconara, OO (original opposer)

----

*Any ideas?*

As an anonymous contributer to the discussion above, I'd love to provide my thoughts on the problem, but I am afraid it's far too abstract for me to even understand what the problem is ;)

--General/AllanOdgaard


----

Yes, please give us an example (better than the General/NSDocument/General/NSUndoManager idea).

In my opinion, most wrapping basically becomes a high-level pointer that limits access (General/NSController/General/NSObject and subclasses). In the same vein as General/GettersAndSettersAreEvil, wrapping a class merely to pass messages (something I find myself doing occasionally) is not a good thing. However, if you create actions that actually do something beyond just sending the message to the wrapped class (such as addItemToDictionaryWhileAlertingTableViewAndSynchronizingWithFileAndDoingSomethingElseRelatedToTheGeneralFunctionOfThisWrapperClass:), it might be a good alternative to categories (which shouldn't override methods that are already implemented) and subclassing (which doesn't work for anything built into Cocoa that isn't supposed to be subclassed, such as General/NSMutableDictionary).

--General/JediKnil

*addItemToDictionaryWhileAlertingTableViewAndSynchronizingWithFileAndDoingSomethingElseRelatedToTheGeneralFunctionOfThisWrapperClass breaks the rule of General/HighCohesion. Code written in this manner is very low grade and messy. Methods should do one thing, no more. Wrapping for adding actions is not really wrapping, that is more like what we do all the time, using the ivars of our classes, we just don't say it because it's obvious. Wrapping should be more common, it's useful for building shallow class-hierarchies and protocol based hierarchies (read General/ExtendsIsEvil and General/WhyDeclareClassAsAbstract for more info on this).* --General/TheoHultberg/Iconara

That was an example (albeit a bad one). The point was that if you always (for example) have to notify observers when an object (say, an General/NSMutableString) changes, as well as log the new version to a file, it might be easier to come up with a method that does this. Of course, you'd want to keep a reference to the file. Does it make more sense to add this method (and file reference) to one of the observers, or a intermediate class created for this purpose? (You know, the two of us have also been posting on a very similar issue in General/WhyDeclareClassAsAbstract...) --General/JediKnil
