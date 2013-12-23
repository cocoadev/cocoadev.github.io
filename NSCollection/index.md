---
layout: page
title: NSCollection
---



Nonexistent abstract superclass of NSSet, NSArray and NSDictionary, which would define abstract method -objectEnumerator. 
If it existed, we could add other common collection methods to it using categories. I don't think this class will ever come to be, I'm just pointing out the current situation isn't ideal.

Why do this? Well, its probably good refactoring.

Java implements this class in it's collection heirarchy.
See http://java.sun.com/j2se/1.4.2/docs/api/java/util/AbstractCollection.html. 
Of course, you couldn't extend this quite the same in java, since java doesn't have categories.

Currently to extend all collections with a method, we can:

*Use NSObject as the superclass and add the method to NSObject. NSObject is not a collection, so you may need to check at runtime that it responds to collection methods you require in order to extend. This goes against OAOO slightly, because you may have to check this for every method you add. It also means the compiler misses errors where an object and a collection have been confused, although these errors should be cleared up in design and are non-issue if you are viewing NSObjects as collections with only one object.
*Or, add the method to NSEnumerator, which means you have to get the enumerator from the collection every time.
*Or, add the methods to each individual collection class, which goes against OAOO. For one extra piece of functionality, you have to add as many methods as there are collection classes. Even if we are only talking NSSet, NSArray and NSDictionary, that is still 3 times the chance of making an error.
*Use CocoaSTL. This requires renaming all your files with .mm suffix and knowledge of STL and C++.

add to this list any I have missed.

The point is there isn't really a single place to define common collection functionality.

IMHO the best current solution is to use NSObject.

ObjectAsCollection discusses this.

See also http://www.c2.com/cgi/wiki?OnceAndOnlyOnce  
--MikeAmy

----

You might take a look at CocoaSTL it provides a way to write algorithms OAOO which can be used with containers -- and it even exposes them to all the existing algorithms present in the standard C++ library.

----

*If you really want to make a container hierarchy where the superclasses implement general functionality, it will be a very tangled class hierarchy most likely requiring multiple inheritance.*

Nothing really requires multiple inheritance. Instead one can use delegation. See http://www.c2.com/cgi/wiki?MultipleInheritanceIsNotEvil 

*I do not see how you can logically put NSString, NSIndexSet, NSData, NSTextStorage, NSSet, NSAttributedString, NSDictionary, NSArray and NSTree (i.e. CFTree) into a class hierarchy where the super classes provide common functionality inherited by the sub classes. Either you have very few super classes and have to re-implement a lot of stuff in the sub classes or you do the opposite. But when doing the latter, it might be very hard to make this class hierarchy, because sometimes a string is the same as an index set (e.g. they both store primitive types), other times an index set is similar to a tree (they both have an order) etc.*

*Furthermore, many of the common container algorithms will also be useful for 3rd party container classes -- but you will most likely design the hierarchy with only the existing classes in mind, making it a mess to get anything "from the system" when writing own container classes, because they do not fit into the current hierarchy.*

*I might be wrong, but so far all the attempts of creating container class hierarchies that I have seen, did not provide any savings when having to implement the actual sub classes, and so, the entire hierarchy is useless because we already have informal protocols.*

----

I don't think I fully understand what is wanted, and I don't understand from a cursory read of the linked document what the difference between OAOO and KISS and generics really are, but I agree the CocoaSTL stuff sounds like it comes close.

*I think KISS mainly relates to user interaction, OAOO deals with programming -- neither provides a description of a method to obtain the goal (i.e. they are abstract teachings like: "do not do anything I would not have done"). Generic programming does try to provide an actual way to achieve OAOO, although OAOO is not really the goal of generic programming, e.g. we may have specialized (optimized) version of various algorithms, although not visible by the user of the library.*

*For the records: generics is the name of Javas way of reducing typecasts in the language and obtain better compile-time type safety.*

*IMHO KISS - (Keep it simple, stupid!) is the parent idea of once and only once, that is, doing things once and only once keeps things simple* 

Which method are you talking about adding to NSObject or the collection classes themselves?  Do you mean all of the methods you need to do something?

Why not just add a category with "-(id)asCollection" which returns an instance of a new "MYCollection" class, containing all of the logic you need.  On NSObject, it returns nil or an instance of some defunct implementation. On each of the three collection classes you mention (NSSet, NSArray, NSDictionary) , it returns an instance of some subclass of MYCollection which  deals with the class at hand.  All of the extra methods you need to do all of the work can be defined on MYCollection, or some class in a completely separate hierarchy.  (You could add similar methds to get a specialized enumerator of some sort, if you needed such a thing for your collection methods.)

This sounds to me pretty much the same as you propose, except that the notion of whether a given class is seen as a collection is divorced from what the the class hierarchy happens to be.

----

The methods referred to in the original post are those that are common to all collections, including default implementations, and abstract methods. Not methods that are using specific properties of the collection, for example, addObject: , which sometimes takes a key.

For example: 
    
// default IMP checks all objects returned by [self objectEnumerator]
+ containsObject:
// joins all objects returned by [self objectEnumerator]
+ componentsJoinedByString:
- makeObjectsPerformSelector: 
- reverseObjectEnumerator:
- (BOOL) isEmpty

etc

Stefan gives a better list  below.

Using -asCollection - wouldn't you still have to rewrite method -asCollection for every collection class defined, either splitting it up with a switch (not good) or adding a version of - as<Collection> for every <Collection>? Neither of these solutions are once and only once solutions.

Along similar lines, one could add default implementations of the category's methods to NSObject, to make it act as if it were a collection with only one element, but that is resorting to adding methods to NSObject again, as described in the first point.
For example, NSObject's -objectEnumerator could return an enumerator that only contained self. 

- MikeAmy

----

Because NSSet et al were not first created with your particular OAOO application in mind, it will be necessary to either switch on the class type or to add a method to those collection classes so that you get an enumerator that does what your collection class needs.  The less intrusive of these, it seems, would be to add one method to each class that knows how to present itself as a collection for your purposes.  The superclass of all of your collections will implement whatever methods you need on the collection, and the subclasses would simply take care of accessing the data structure with which it works (NSSet or whatever).  This is no more non-OAOO (as far as I can see) than adding new implementations for object enumerator.

There will be no switching on class type necessary, and no specific as<Collection>.  When you call -asCollection, it simply makes an instance of some subclass of MYCollection which takes over whatever MYCollection methods are needed to work with the receiver of the asCollection message.  For your example, you add four -(id)asCollection methods:


*one on NSObject, which either returns nil or an instance of some subclass of MYCollection that will work with general objects
*one in NSSet, which takes over whatever methods on MYCollection it has to to operate on a set's elements
*one in NSArray, which takes over whatever methods on MYCollection it has to to operate on an array's elements
*one in NSDictionary, which takes over whatever methods on MYCollection it has to to operate on a dictionary's elements


I thought about an enumerator for NSObject that returns only one object, but this would be a little bit inconsistent with those classes which provide a ture enumerator.  However, this could be swapped in for the "defunct" case I mentioned before.

However, if you're only talking about the methods that walk forwards and backwards through an enumeration of the collection's elements, for read-only access,I see no reason for them to have a common parent in the class hierarchy.  All of the classes you mention already include an -objectEnumerator method anyway.

What is the problem again with just adding the few functions you want to NSObject?  There is no need to check at runtime whether the class is a collection.  If the object has no implementation for -objectEnumerator, it will return nil, which will return nil for any methods called upon it.  If you don't like this situation, just add an -objectEnumerator to NSObject and be done with it.

As for confusion between collection and non-collection objects, this comes down to a different philosophical argument.  I would argue that this sort of confusion would be cleared up in the design process.  The compiler can be counted on to catch some typos in your code, but policing the difference between collection and non-collection objects is really something that should have been done before coding even begins.

----

Say we take another method : -(BOOL) isEmpty. The implementation could be { return [self count] == 0; }. What are we going to do, add this exact same implementation to every class? Can you see this being reimplemented more efficiently by a collection subclass? If not, it belongs in NSCollection.

I agree with the point about object|collection confusion being sorted out in design, it was really a side issue, but still an issue. If the compiler can detect problems, that has usefulness. If NSObject implements objectEnumerator, etc, it's a non-issue. I've added this to the original post.

Is not the declaration of common abstract methods a good enough reason for classes to have a common parent? NSProxy for one seems to think so. At the very least it means less documentation to leaf through.

nil returns from non-methods might not work happily in every case, although they may do in single cases such as -objectEnumerator. There is usually a better solution, explicit checking for nils at the least. They are nice though. 

As for me, I'm going with the idea of NSObject as a single element collection, stick with adding methods to it and be done with it. 
However I still feel the point, that the current situation is not ideal, is still valid. NSObject is not generally perceived as a collection. 
In the light of this discussion, I have changed the original post. Thanks - MikeAmy

----

I don't happen to think *the declaration of common abstract methods* a sufficient reason for classes to have a common parent.  It is a convenience to be sure, but we have to keep in mind that we are limited to single inheritance here, and it is very possible that you might want to treat another object (e.g. an NSTableView) as a collection. The framework is limited to single inheritance, so it does not make sense to let a few common abstract methods define the hierarchy.  Had we the luxury of muliple inheritance, something like this might make sense, but functionality and a single perceived role for a group of classes cannot dicate the inheritance tree.*

Actually, I was a little incorrect with nil returns.  I think you'll actually get a runtime exception if you try to call -objectEnumerator on an object which does not support the method.

----

*it is very possible that you might want to treat another object (e.g. an NSTableView) as a collection*

I think this sums up the greatest problem with OO � don't get me wrong, OO is great for e.g. a GUI kit, but I do not think it gives any advantages for e.g. algorithmic programming. Previously it was mentioned that something like     containsObject: should be implemented in the NSController superclass, and it could be so, given that all subclasses implement     objectEnumerator, but let me change that example a bit:

We write a text editor using NSTextStorage as backing storage. Then we write a file/hex editor with NSData as backing storage, and later we write something which needs to do a simple "parser" on an NSString.

In all of the examples we most likely would need to search our backing store for a range (i.e. string) initiated by the user/GUI in the two first examples.

So should this "implementation" be in a common superclass to NSString, NSData and NSTextStorage? neither of these are currently seen as collection classes/containers, but the above is IMHO a collection algorithm.

If we persue the OO design we quickly end up with bloated superclasses, and we also end up treating a lot of primitive types as objects with a constant need to wrap/unwrap the actual types (so that e.g. an array and corresponding algorithms work with both integers and objects).

For the latter I think Java is already there... Cocoa is not, because Cocoa has mainly dealt with GUI, and I think that many do not make extensitve use of ObjectiveCee for the model part of their application, although I might be wrong here. 

*IMHO bloated superclasses are a codesmell. Using OAOO, they stay nice and slim. Extra functionality is added with equally slim categories. A useful metric is that a bloated class has more than 10 methods. From this we can see most of Cocoa is bloated.* 

----
If we look at Java collections we can get an idea of what we might do as a superclass for NSSet, NSArray and any others. NSDictionary is different. It is a collection but a collection of pairs or a map as it would be in Java. You could have NSDictionary as a superclass but you would have to make sure you only passed it pairs(NSPair?) this is where templating(Generals? in C# and Java 1.5) come in handy.

IMHO you would have NSCollection with the following methods:
//Primitives

*BOOL add:(NSObject)�o
*BOOL contains:(NSObject)�o
*void clear
*BOOL isEmpty
*Iterator iterator
*BOOL remove:(NSObject)o
*BOOL equals:(Object)o
*int size


//Non-Primitives 

*BOOL addAll:(NSCollection)c
*BOOL containsAll:(NSCollection)c 
*BOOL removeAll:(NSCollection)c
*BOOL retainAll:(NSCollection)c
*NSObject[] toArray:()


As far as I can tell this would work with anything you wish to be a collection and, well I guess you could argue that if you cannot implement all of these then it's not a collection(I'm sure that people would disagree). How about making this a protocol instead of a superclass? *How about both as in Java?* It is a bit of a pain because you loose the advantage of having primitive and non-primative methods but you could make everything you wanted an NSCollection. Improved the structure but lost the OAOO. You may say, "Well what's the point then?" and I would reply with the question, "What's the point in having any protocols at all?". Still not perfect but a little better.

You could also go on to having a NSMap protocol for NSDictionary and any other mapping structure you can think of. 

I guess one of the problems with the current implementation of Cocoa is that it is not concerned with how something is implemented just that it is implemented. Possibly the reason there is no explicit NSLinkedList, NSHashMap or NSTree. Yes, all may be implemented by NSArray, NSDictionary or NSSet but you don't have to concern your self with their implementation or their complexities. Showing that cocoa was never designed for writing algorithms with. If it was meant for algorithms would the runtime be as it is? I doubt it.

Stefan

*When does something stop being an algorithm?  Surely the best way is to implement the high level stuff in OO, and, if really necessary, implement, for example, performance critical mathematical algorithms in C using a maths library? This can still be done from a Cocoa environment.*

----

I do not think that "we" (at least I) by algorithm refer to performance critical parts. Something like     containsObject: or     containsRange: is an algorithm.

You can (as shown by the StandardTemplateLibrary) write generic algorithms which can be applied to arbitrary collections (sequences) -- in the StandardTemplateLibrary there is (with a few exceptions) no need for a collection class to implement member functions (methods) other than those who return iterators, and most implement only these (and some extra like size, empty etc. -- but these are in fact sort of redundant).

So stuff like sort, random shuffle, find, reverse, copy (subrange) etc. etc. are written as generic algorithms which work on generic sequences (and even old C strings or primitive arrays work with these).

This allows to only write n different algorithms to be used with m different collection classes, instead of having to write m times n different algorithms (i.e. one new implementation pr. collection class).

----

Is there a brief example of STL that can go here? Is there STL for straight Objective-C ?

----

There is sort of STL for ObjectiveCeePlusPlus (which require you change the suffix of your .m files to .mm, and otherwise have no real impact on your sources, except that it now allows CeePlusPlus features).

*So no STL for straight ObjectiveCee?*

If you look here http://www.sgi.com/tech/stl/table_of_contents.html then you'll see most of what is currently in the standard library, that is, stuff you can currently use with GCC.

Generally STL algorithms take two iterators, a starting and an ending point -- these can be obtained (when including CocoaSTL.h) simply by calling the functions beginof and endof. Example:
    
#include <algorithm>
#include <CocoaSTL.h>

NSMutableArray* array = [NSMutableArray arrayWithObjects:
   [NSNumber numberWithInt:1],
   [NSNumber numberWithInt:2],
   [NSNumber numberWithInt:3],
   [NSNumber numberWithInt:4],
   [NSNumber numberWithInt:5],
   nil];

// check if array contains 2, using *standard* binary search
bool hasTwo = std::binary_search(beginof(array), endof(array), [NSNumber numberWithInt:2]);

// shuffle the array with a *standard* algorithm:
std::random_shuffle(beginof(array), endof(array));

// reverse the array with a *standard* algorithm:
std::reverse(beginof(array), endof(array));

// rotate the array with a *standard* algorithm:
std::rotate(beginof(array), beginof(array) + 2, endof(array));

...


There are dozen of algorithms like the above in the standard library, and they can (more or less) all be used with Cocoa containers, by the use of CocoaSTL. And here containers also include NSString, NSData and NSIndexSet (where neither work with 'objects', and thus would fit badly into an NSCollection scheme).

There is another form of iterators, namely output iterators, these are used for "writing" to, e.g. the result of an algorithm, and such an iterator can be obtained with the function back_inserter, again giving the container as argument. Example:
    
char* src = "Hello world";
NSMutableString* dst = [NSMutableString string];

// copy string using *standard* algorithm
std::copy(beginof(src), endof(src), back_inserter(dst));

// copy all but 'o' characters into dst (standard algorithm)
std::remove_copy(beginof(src), endof(src), back_inserter(dst), 'o');

// copy all but replace space with underscore (standard algorithm)
std::replace_copy(beginof(src), endof(src), back_inserter(dst), ' ', '_');

...


I only used NSArray and NSMutableString in the above, but it works the same for all collections (which fulfill the requirements of the algorithm, e.g. std::sort needs the container to allow for random access and that it is mutable etc.).

Btw: in the above, if you change the     char* src = "..." line to     NSString* src = @"..."; the code will produce exactly the same result... this is one of the goals with generic programming, i.e. to isolate the algorithms from the data storage, allowing for much greater flexibility (i.e. applying an array algorithm to a string etc.) -- for example the other day I had a list of NSRects making up the selected portion of my NSView, I needed to take the union of all these rectangles which would normally be a for-loop, but the algorithm here is really std::accumulate (i.e. add together the rectangles), but using NSUnionRect as the binary add operation -- most STL algorithms allow one to (optionally) provide the "operator" to use as an extra parameter, so all I had to write was:
    
// first the range, then initial value, and then binary function to 'add' to elements
NSRect clip = std::accumulate(beginof(rects), endof(rects), NSZeroRect, NSUnionRect);

Another place where I need to work on these rectangles is when the user clicks the mouse in my view, when the window is not key (as that should initiate a drag operation, if he clicks in the select), here the algorithm is std::find_if, i.e. we wish to find a rectangle in the sequence which contain the mouse point -- if we find it, the user clicked inside the selection. In code that is:
    
NSPoint p = [self convertPoint:[theEvent locationInWindow] fromView:nil];
return find_if(beginof(rects), endof(rects), bind1st(NSPointInRect, p)) != endof(rects);

Here find_if (a standard algorithm) takes first the sequence, then the unary (i.e. one argument) function which should return true, if we have a match. I use NSPointInRect, which takes both a point and an NSRect, but the point is always the same (and find_if expects the function to take only one argument), so I use bind1st (also from the standard library) to bind the first argument to the point, resulting in a new (unary) function which does what we want.

Finally I test if the result is equal to the end of the rectangle sequence, which means we did not find a rectangle to which NSPointInRect returned YES.

My program is literally filled with such use of standard library algorithms, and it really keeps down the number of lines...

I hope I have managed to inspire some to give STL (and CocoaSTL for that matter) a chance! it took me at least a year before I really started to grasp all the ideas with generic programing, so give it time, but give it a chance! :)

*Why do you not think that STL provides a solution to the OAOO problem when having to implement container logic?*

STL does provide a solution, but there could be another solution that doesn't involve mixing yet another C-derived OO language into the foray. After a bit more looking into STL, it seems to me that templates aren't really necessary in a dynamically typed language, such as ObjectiveCee, C++ needs them since it lacks the dynamic typing of ObjectiveCee. Java needs them too and there's an implementation in JDK 1.4. ObjectiveCee doesn't need them as it has dynamic typing. Notably Smalltalk doesn't have them either. Why should I use ObjectiveCeePlusPlus when ObjectiveCee will do, and if STL is so great, why hasn't it become accepted in ObjectiveCee? I think many will ask the same questions. 

I'm not saying it isn't a solution, just it seems like a good solution for C++ programmers, rather than ObjectiveCee programmers. 
*(suggests the STL discussion is getting off topic and should be moved elsewhere)*-- Mike


----

I do not know what you mean by "lacks the dynamic typing". C++ does have run-time dispatch. But there are many reasons why STL uses templates instead. One obvious reason is that you can't for example write something which can both work on NSString and NSArray, since one stores characters and the other stores objects...

*What if NSString implements NSArray style container functionality where it works like an array of character object? Then, it would be possible, wouldn't it.*

I think your request about moving this and then at the same type asking that "if it is so great, why has it not been accepted as standard in ObjectiveCee?" shows part of the reason -- many ObjectiveCee programmers do not know about STL and clearly they do not wish to, because CeePlusPlus has a bad reputation among them.

*Careful, they may have reviewed CeePlusPlus and decided it was not for them, but for other reasons. C++ has deficiencies as do most languages.*

Also, ObjectiveCeePlusPlus did not work satisfyingly until recently, so it was really not until that it became a reasonable option. Apple provides us with it out-of-the-box in ObjectiveCeePlusPlus, and if you look at the STL implementation you'll see that Apple has actually added a lot of stuff (amongst other very good debug support), so clearly Apple does have an interest in STL -- but announcing that this is now the standard way to access Cocoa containers would be stupid, because most people do not really need the power of STL in their ObjectiveCee programs, and Cocoa was created before STL, so glue code would be required (like CocoaSTL) -- something like: if it isn't broke, don't fix it!

*If it ain't broke, don't fix it, may be the wrong attitude for refactoring. Instead refactoring mercilessly may be better. Unless say it was really a throw-away program that you only use once.*

When I "push" STL at this page, it is not because I want Apple to announce it as the new standard, I think that would just complicate things for beginners, and I do not think anything good would come from it -- but at this page you are putting forward a proposal to enrich ObjectiveCee, but I do not think that this will enrich it in any way, on the contrary, it will just result in a complex container hierarchy, but I think that the goals of your enriching (and more) is already available through the use of STL, and that is really the proper direction if you need to re-use container algorithm implementations.

*Did I say I wanted to enrich it? Sorry, I really meant that it could be simpler. Also I think fewer classes does not always mean more simplicity.*

Finally let me add, that I actually think that a lot of people write their application back-end (i.e. the model) in C++/STL and then only the front-end in ObjectiveC (at least that was the impression I got after reading a thread at Apples CocoaDevML).

----

Something which people should remember is,  if they are going to use STL with cocoa STL stuff do not retain or a release for you *rephrase?*. So if you put data into your MultiSet you must retain it first and if you remove it from one you must release it. Probably doesn't matter if you never get down to the event loop but still good practice. I would guess CocoaSTL will do this for you.

Also don't think that all the code in STL will work with your Obj-C objects straight away. They will not. STL treats Obj-C object as points. So if it tries to index an NSArray element it will not be using objectAtIndex: (OK it might but you'll be lucky if it does).

ObjectiveCeePlusPlus really is C++ and Obj-C stuck together with C. ObjectiveCeePlusPlus is both dynamic and statically linked depending on whether your code was Obj-C or C++ code.

And STL is great and is very useful although confusing at times. Really it is written for the more technical programmer. If you don't wish or care about complexities and you really don't give a S*** if it is more type safe then don't bother. More then likely you will end up spending the time you save, writing C++ wrappers from you Obj-C classes.

Mike you are quoting Smalltalk and obj-c as cases for not having templates(generics)? Both either did or very nearly did die out. The majority of OO languages which I can think of have generals most of them more modern then Obj-C. You never know they might be included at some point (although I doubt it. It's not java). 

Stefan

*If both obj-c and smalltalk did have templates, why don't they now? And who told me templates were a last minute add-on to CeePlusPlus and the guy who did them would have done it differently? I can't remember. I think his name began with an S.*

My understanding was not that they had templates, but rather that they nearly died out (or did so).

----
From http://developer.apple.com/wwdc/descriptions/descriptions-dt.html where the preliminary descriptions of the WWDC sessions are to be found:

*Objective-C++ combines the dynamic object model of Objective-C with the rich data abstraction facilities of C++ to create the ultimate "power tool" for many programming tasks. We will explore the runtime model provided by Objective-C and then discuss Objective-C++ design goals and philosophy, and Xcode integration and support. We'll also cover issues in performance and compatibility. This session will use real-world programming examples and will cover recent language additions to simplify C++ STL usage. This is an introductory to intermediate-level session.*

So STL is here to stay! :)

*Where does it say that? I only saw it talking about simplifying STL usage.
I've tried STL, ok it works, but I'm still troubled by feelings of confusion. 
I guess I just like things easy to understand. My fault. -- Mike*

