---
layout: page
title: ObjectsStructsAndPointersBasics
---

Why does NSString have a pointer when things like NSRect and NSRange don't? http://goo.gl/OeSCu

----

NSString is a class, while NSRect, NSSize, NSPoint, and NSRange are structs.
The difference is where they're stored: **variables you declare in your methods are allocated on the stack**, including actual pointers...
but **the things that pointers point to are stored on the heap**.

The stack is pretty automatic. If you allocate a variable just after the opening {, then it'll be done with by the matching }, always. The heap, on the other hand, lets you keep objects around basically as long as you want--which is why it's a good place to keep objects. But it makes you manage the memory yourself with things like retain/release and autorelease.

I should also note that it's good practice to use more descriptive titles for your WikiWiki pages. Something like WhyIsntNSRectUsedWithPointers.

-- RobRix


----

NSString is much more complex than NSRect or NSRange. While the latter are merely a convenient name for a few numbers, the former manages its own memory and hides what character encoding it uses for the text you store.

Thus, NSRect and NSRange are simple C structs, while NSString is a fully-fledged ObjC object. For reasons that may become clear as you learn more programming, it is useful to hide NSString's complexity behind a pointer.

Hope that helps some.

----

So how do I know what's a "full-fledged" ObjC object (requiring a pointer), and what's a simple C struct?  I was beginning to think that "NSAnything" was a full-fledged object.  -- a different curious reader

----

You can either read the documentation, or look up the headers (you can hold command and double click on a type to go to the definition of that type, once your project has been indexed). If something is defined with an @interface then it is an Objective-C class.

----

If you look in the class references; then if it's got a page of its own, it's a class. All structs and things are on the types pages for AppKit and Foundation en masse. -- RobRix

----

**further discussion of the meaning of pointer operator and objects (was P**'ointerOperatorDiscussion)**

What is the meaning of *) as often used in:

+ (NSString *)description

and

- (void)forwardInvocation:(NSInvocation *)anInvocation

In the case of the NSString, how would you read that in English?

Thanks in advance -- jh

----

The * is a normal C pointer.  So *+ (NSString *)description* literally means:  *a class method that returns a pointer to an NSString object*

Is that what you meant?

--JeffDisher

----

Yes, thank you! So I assume that by that same logic, in the second example, anInvocation is a pointer to an NSInvocation object being used as an argument to forwardInvocation?

jh

----

Yes:  anInvocation is a pointer to an NSInvocation object, and the     +[NSObject description] method returns a pointer to an NSString object (and happens to be a class method, but this is unimportant).

You'll see the pointer whenever the variable is.. a pointer. :-)  Almost every time you deal with an object in objective-c you'll be holding onto it via a pointer to the object.  In rarer circumstances you may see pointers to other data types.  For example, in the method on NSMutableArray declared as

    - (void)removeObjectsFromIndices:(unsigned *)indices numIndices:(unsigned)count

the first argument is a pointer to unsigned integer (this is the plain C way to pass an array).

Your basic options regarding the eternal mystery of pointers are these:

1) Ignore it for now, and realize that every time you have something you're thinking of as an object, you actually have a pointer to the object and you need to declare the variable with a *, or

2) Read the chapter on pointers from any standard C book.

I guess I'd recommend 1) for now, with the caveat that you'll want to do 2) no later than a month after you start cocoa programming.  Others would probably recommend learning C in its entirety before learning objective-c/cocoa (C is a very small language, so this doesn't take very long).  One thing I do not recommend is to try to piece together bits of pointer information by asking questions.  This is one of those basic topics where it's best to go find a book. :-)

----
Lots of stuff in C still keeps surprising me: for example, I had NO idea that the comma operator even existed until recently -- even now I can't really think of when to use it.  There is lots of less esoteric and arcane stuff in C that lots of people don't know about or use: eg bitwise operatorsr, or the question mark (ternary) operator -- a funky, but useful shorthand. 

*Dunno.  But there aren't that many surprises in C because it's so small.  If you go back and reread your C book now, you'll probably come out of it knowing everything there is to know about C.  Another interesting thing that one can miss the first time through is the utility of pointers to functions.*

----
Cocoa is written in C and the particular construct in question is found in virtually every Cocao program I have found. -- jh

----

This is definitely a topic that is easier to learn from a book.  Sites like this are great for patching in small questions.  "What is a pointer" is going to be harder to learn from us, but there are very good answers in the books, and it isn't like the chapter is going to be long.

----
I understand your point, but as originally stated, I did consult 3 books including Kochan's, but none of them answered the question or didn't answer it where I thought it should be, but luckilly I got the answer here in short order and I appreciate that very much. -- jh

----

There is an entry point to any skill.  C is the entry point to Objective-C and Cocoa.  The pointers to cocoa objects (e.g. NSString *string) are no different than straight C pointers to structs -- in fact, that's just what they are.  If you aren't at a point where you feel comfortable with pointers, you will not be doing yourself a favor by jumping in head long into objective-c or cocoa.  Moreover, Cocoa is essentially nothing more than an API, and while we do have some more abstract discussions here about design patterns, the site's primary purpose is to help each other with the API, which is very very rich and takes years to really master.  As one of the posters mentioned above, ANSI C is something you want a book for -- you want an organized and systematized approach to learning something as small and fundamental as C.

----

*Actually, I disagree with a little bit of this, as I think you can start learning cocoa/Obj-C before you learn C.  For example:  We do use pointers, but we rarely dereference them explicitly.  It isn't really crucial to know what's going on there, at least at first.  That's why I kind of suggested taking the "ignore it for now" approach.*

----
I will also mention, that there is no imaginary line, jh -- C is one language, Objective-C is a superset of C, and Cocoa is an API -- all very realy and tangible distinctions.

----
I'm amazed nobody has pointed it out yet, but talking about *) is extremely inappropriate grouping. Parentheses are simply ways to create precedence, and are always grouped. In this case, you have (*thing*), where *thing* is NSString *.

* ... which, in this case, "NSString *" is read, "NSString Pointer". When encountering the "*" in C or Objective-C, always read it as the word, "pointer" in your mind. That helps tremendously in identifying what you're reading if you get in that habit. At least for me, anyway. *

Pointers are also used as such:

    

// You can do it this way
NSString * blah = @"Blah";

// or this way
NSString *blah = @"Blah";

// or even this way
NSString * blah = [[NSString alloc] initWithString:@"Blah"];



  It all means more or less the same thing.

*As with all operators in C, whitespace has no meaning. NSString*blah is the same as NSString    *    blah.*

The nitipicker in me points out that because of C's greedy parsing rule there's a difference between     foo = 37 / *bar; and     foo = 37/*bar; -- the latter will be interpreted as a comment by the preprocessor.  But in most cases, whitespace has no meaning.

----

Finally, to answer the original question a bit more completely,     + (NSString *)description is a ClassMethod, called     description, that returns a Cocoa object, specifically an NSString (or something derived from an NSString).

----

**Is it possible to check if a pointer is valid**

I want to be absolutely sure that an object I am passing to a particular method is initialised and everything is ok with it.

My tests for the object so far are as follows:

    

- (void)myImportantMethodWithNetService:(NSNetService*)netServiceToConnect
{
if (!netServiceToConnect) return nil;
if (netServiceToConnect addresses] count] < 1) return nil;

...

Do something with valid 'netServiceToConnect'



However I can still get past the above by sending a variable created in the following way:

    

[[NSNetService* fakeService; //(No init - just pass this on)



How do I check that a variable I have been passed is properly initialised for use? Checking for nil won't rule out all errors.

----

Checking for nil is inappropriate, since [Class alloc] by default returns a legitimate pointer.  You could potentially have a magic number 
    
#define  MY_MAGIC_NUMBER 23842 // or any other number you want provided it's larger than 8

and then you can quiz the object to see if the magic number ivar has the appropriate magic number set (which gets set inside any and all of your init methods.

If you're doing this for debug (i.e. you forgot to stick an init somewhere), then just do a find for [MyClass alloc].

----

There is no way to detect if a pointer is valid.  That is why stack-based allocation of variables ALWAYS needs to be initialized to a safe value.  Here, for example, you would want to set fakeService to nil on the line where it is declared.

Note that all data in an object is initialized to 0 by the Obj-C runtime and all data allocated statically in the binary will be initialized to 0 by the OS's loader unless a different value is specified.  Stack variables, however, will be whatever was in that chunk of memory last time it was used.  ALWAYS initialize variables to something safe (you have no idea how many software bugs are caused by assuming stack-allocation will be 0-ed)!

----

I'm just worried that an NSNetService object that is returned by a notification automatically from my Rendezvous implementation is sometimes 'rogue' and not a proper NSNetService, and could cause my app to crash. Seeing as I am not creating the NSNetService, I can't check that it has been initialised properly.

----

If the framework is passing you an object, it will either be nil or a valid object (whatever that is in the given context).  Apple is good enough not to pass uninitialized pointers to user code.  In the specific case of NSNetService, it can be thought of as the sender (basically) so it will definitely be valid. If it isn't valid, it's a bug: go to radar.apple.com and report it.

see also MemoryManagement

