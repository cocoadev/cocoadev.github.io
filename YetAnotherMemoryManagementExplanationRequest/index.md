---
layout: page
title: YetAnotherMemoryManagementExplanationRequest
---

Sorry to ask this     autorelease question for what must be the pow(n, n) th time...

1) Object A implements a     description method in which the returned string is (assumed to be) autoreleased

2) Object B asks Object A for that description as follows:

    

// This works fine, because (??) the text view's General/NSText object is retaining the string for us
[ objectBTextView setString: [ objectA description ] ];  



3) A long time ago, in a galaxy far, far away, Object B asked Object A for its description without really knowing what he was doing

    

General/NSString *descripString;

// This does not work, because we did not do descripString = [ [ objectA description ] retain ];
descripString = [ objectA description ];  
// Fails, because (??) the autorelease pool is, como se dice, "drained" (?) after the above line 
[ objectBTextView setString: descripString ];       



If my (commentary) analysis is correct, then I believe I finally understand what the autorelease pool is on about, in its heart-of-hearts
Well? Is it?

From my introductory reading, I have been led to believe that this autoreleased object would last long enough to get to the next line of code...

----
descripString will remain in the auto-release pool until the auto-release pool is recycled/destroyed or if the string is subsequently retained. The auto-release pool can be cleaned up in one of two ways; by releasing an auto-release pool of your making that was active during the     description call, as most often seen in command line Foundation applications or, as is it seems to be in your case, when the call stack gets back to Cocoa's internal event mechanism. As long as I am not missing anything in your example and the General/NSTextView (actualy a General/NSText method) retains the string, the code as presented should be fine.
----
Temporary variables don't make any difference in the resulting code. The following pieces of code will result in exactly the same behavior, and probably exactly the same machine code:

    
[a setThing:General/b getThing] modifyThing;

id temp1 = [b getThing];
id temp2 = [temp1 modifyThing];
[a setThing:temp2];

----
I don't think it was the temporaries/assignments he was woried about, it was the lifetimes of the referenced objects, although I could be wrong. *He posted two pieces of code that were identical other than temporaries, claiming that one worked and one didn't. I was just clarifying that the two situations and the object lifetimes involved are the same.*

Both pieces of code are identical, by the time they get compiled. (The compiler will have to use a register as a temp, anyway. It doesn't make any difference if it has a name or not.) If the behaviour is inconsistent, something else is wrong. Autorelease pools aren't emptied by magic -- the app needs to actually run code to do it, and that can't happen while your method is executing. Please show us your real code.

----

Using the strategies I presented (which do not differ, as you remind me) does not in fact cause an memory error
The crash is caused by the following scheme

    
- ( General/IBAction ) displayTheResult
{
General/NSString *descripString = [ [ General/NSString alloc ] init ];
...
descripString = [ thing description ];
[ theTextView setString: descripString ];
...
[ descripString release ];
}


The crash is avoided by

    
- ( General/IBAction ) displayTheResult
{
General/NSString *descripString = [ [ General/NSString alloc ] init ];
...
//descripString = [ thing description ]; //never mind, don't need this line, it screws things up
[ theTextView setString: [ thing description ] ];
...
[ descripString release ];        // Cleans up an utterly superfluous alloc-release pair
}


There is still something going on here that I do not entirely understand. The text view is fine with
the string object (autoreleased) returned from the description method, but does not seem to
be able to retain the object that we allocate, set into the text view, that we later release explicitly.

I guess the retain given the object by the General/NSText in the text view does not overcome releasing
the object  at the end of the method. Or maybe there isn't a retain after all......?????

I was under the impression that the retain that descripString gets in the text view will increment its retain count and keep the
string object from being deallocated.

----

I beleive the text view copies, not retains its string, since it needs it to be mutable. So we're talking about two different objects here. The crash (at least in the last code example above) is the normal crash you'd get whenever you explicitly release an autoreleased object.

----

Ah! It figures! Even though I allocate a string object, when I assign an autoreleased pointer to it, the retain count belongs to
whatever object that pointer currently points to.

The introductory books just say that alloc increments the retain count and release decrements it. Of course, it is the OBJECT
that has the retain count and not the pointer! It is simply NOT invariably true that if you create an object with an alloc message
to its class, that you MUST at some point send it a release message in order to avoid a memory leak. And yet the introductory
books gloss over this point, making it seem like you should do what I proposed above.

I'm embarrassed because this was not really a question about what alloc and autorelease do, but what a pointer is. AAAAAAAAARRRRRRGHHHHHH!!!!!

----

*It is simply NOT invariably true that if you create an object with an alloc message to its class, that you MUST at some point send it a release message in order to avoid a memory leak.*

Well, yes, it IS true. If you do the following:

    

General/NSString *someString = General/[[NSString alloc] init];
someString = [somethingElse description];



you're leaking the original string object. And there's no way to fix it after the reassignment, since you no longer have any reference to that object. You'd need to release someString first, which results in some pretty obvious do-nothing code.

    
General/NSString *someString = General/somethingElse description] retain];
//do stuff with someString
[someString release];


----

Hmmmmmm. I stand corrected. And chastened.

"Those who do not learn from history are condemned to repeat the mistakes of the past."

or

"I don't know but I've been told, autoreleased objects don't get old."

----
Egads, I almost had an aneurism when I saw your replacement code! Fortunately someone else put you on the right path :-).

----

I think all has been clarified to me (the original questioner). But, um, what was the aneurism-threatening "replacement code".
Losing a pointer is something that I have not worried about so far since at my elementary level I have not had much occasion to pass
autoreleased objects around my apps.

One danger in Cocoa for tyros like me is that the memory allocation is SO nearly-taken-care of
that unless you know what you're doing, you don't know what you're doing (so to speak).

In comparison, that is, to the handles and moveable blocks from the old toolbox days
- which I never wanted to touch with a stick, tyro that I am!

I don't care what anyone says: I'm not going back to Java. I'm not! I'm not! I'm not!

And if it's all right, I will orphan this discussion forthwith.

- The part that concerned me was the leaking as mentioned above, which was why I asked you to provide your real code, which revealed the problem. If not auto-released (objects) or freed (malloced), in C-family code, if you lose the pointer, you lose the memory. In this case it was rather benign, but in a loop, you'll soon experience aneurisms of your own. I don't blame you for confusion in trying to learn both OOP and C-style memory management at the same time, but this is why I usually softly suggest that newbies learn pure C before Objective-C -- not 'cause OOP is hard, but because pointers and memory are hard, and best dealt with before being given the power to create objects. Otherwise, start with Java or Ruby or another safe OOP playground. 

----

Agreed. My C is a bit rusty since I spent a good while focused on Java before turning to Cocoa and [[ObjectiveC. Should have brushed up a bit, I guess.

Anyway my new Cocoa-related question is this:

If I create an autoreleased mutable array object inside one method, return it as type (General/NSArray *)
and assign it to an General/NSArray pointer variable declared in the caller,
(no pre-existing object this time, just the pointer - no more lost pointers for me!)
will it actually **be**
immutable in the environment of the caller, or do I have to do some sort of copy out there in the caller?

And yes, I understand that I have to do a retain (or copy?) out in the caller if I want to do anything subsequently with the array.

I know you can do mutable copy to keep an object mutable - so if I do an ordinary copy, do I get an immutable object?
Or an irresistible coerce?     ;-)

----
*so if I do an ordinary copy, do I get an immutable object*

In the case of General/NSArray/General/NSMutableArray, Yes.

For further explanation of why I say "*In the case of*", see the following

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Protocols/General/NSCopying.html
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Protocols/General/NSMutableCopying.html

It basically states that if an object to be copied makes a distinction of both immutable and mutable variants then adhere to both the General/NSCopying/General/NSMutableCopying protocols respectively otherwise only implement General/NSCopying, immutable or not.

BTW, If you have an acquired an auto-released object of the correct type in your method (that you want to return) and your method contract says it will return an auto-released object, you do not need to     copy/    retain/    autorelease it, you can just return it.

I will also point out that a method implemented as such...

    
- (General/NSArray*)doSomethingReturningAnArray
{
    General/NSMutableArray *array = General/[NSMutableArray arrayWithCapacity:n];
    .
    .
    .
    return array;
}


Returns a Mutable array that can be modified further with a simple      General/NSMutableArray *array = (General/NSMutableArray *) [someObject doSomethingReturningAnArray]; .

I point this out as an example/warning of something that should NOT be done, not something that CAN be done. When dealing with callers outside of your control, or even in your control, code defensively. So if you intend to return a General/NSArray, return an General/NSArray.

----

Thank you. Those last remarks exactly address the problem I suspected. I take your words to mean that I should do something like

    
- (General/NSArray*)putNumbersInArray
{
    General/NSMutableArray *array = [ [ General/NSMutableArray alloc ] init ];
    ...
    do
    {
          [ array addObject: [ General/NSNumber numberWithInt: nextNumber ] ];
          // etc.
     }    while ( "nextNumber meets my criteria" );
    ...
   // This also gives me an autoreleased array to return
    ** General/NSArray *defensiveArray = [ General/NSArray arrayWithArray: array ]; ** 
   ** release array;**
   ** return defensiveArray;**
}


----

You don't need to declare a new General/NSArray at the end. You can replace the last 3 lines with     return General/array copy] autorelease]

Actually, you'd also need to change the first line to     [[NSMutableArray *array = General/[NSMutableArray array] to avoid leaking that.

----

In my application, I am declaring this as a kind of accessor method (with an appropriate signature), 
but with an integer argument that seeds the computation of a sequence stored in the array.

Is it true that accessor methods to return (rather than set) objects do not normally take arguments?

 Thus I suspect it is not doing what an accessor method is normally expected to do.
In a class intended to be reusable or in a framework, this might be better pitched as an initializer method, declared 

    
- ( id ) initWithSeed: (int) seed;  // or with a specific type (General/NSArray *) as the return value?


Would users understand such a class better if it were declared this way?
(I understand this example is pretty useless except to me; this is a stylistic, rather than technical, question.)

----
*Is it true that accessor methods to return (rather than set) objects do not normally take arguments*

An accessor *get* method should not take an argument.

See: http://developer.apple.com/documentation/Cocoa/Conceptual/General/KeyValueObserving/index.html

The init method is fine, as long as it is truly an init method. You may want to browse the following for more information.
http://developer.apple.com/documentation/Cocoa/Conceptual/General/CodingGuidelines/index.html

*Would users understand such a class better if it were declared this way?*

Yes, as long as it returns a class of the type it was intended. In your case, you want to return an General/NSArray, it should be a category method for General/NSArray.

For your own objects. I guess you could also have a      - (void)setSeed:(int)seed;  method that re-factored some internals. But you could have a separate, non-accessor method, clearly named that did what you wanted, like      -(General/NSArray*)refactorTheWorldWithSeed:(int)seed; .

----

This morning I woke up having resolved to implement an accessor method (void) setSeed: ( int ) seed

Now I know where I got the idea. Thanks again for your considerable patience in explaining things to me.

As for refactoring the entire world with my seed, my only response must be "Had I world enough, and time..."

Hello? Monsanto?
