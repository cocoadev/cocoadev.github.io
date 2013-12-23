---
layout: page
title: ObjC
---



Objective-C is the primary language used for programming with Cocoa.  It is based somewhat on the SmallTalk programming language.

(You can also program Cocoa in Java, but there can be speed issues: http://www.bignerdranch.com/Java.html )

Objective-C consists of a set of Object Oriented extensions to the C language, featuring things like DynamicBinding and a powerful RunTime system. An Objective-C object (AnObject) communicates by sending ObjCMessages.

ObjectiveC is a superset of C. That means that virtually all C-code compiles in an ObjC compiler. C++ is another C superset. (Actually, C++ is not a true superset of C which can lead to annoying problems (see below)). The nice thing is that the respective syntaxes of Obj-C and C++ do not intersect. This is where the (arguably unusual) syntax of Obj-C pays off big time because it doesn't conflict with anything in C or C++. That leads the way for ObjectiveCPlusPlus, which consolidates all the features of the three languages (albeit with some growing pains evident now and then).

We (users of MacOSX) compile Objective-C using the GnuCompilerCollection. There are other compilers (albeit not many), including the PortableObjectCompiler.

ObjectiveC was created by BradCox - http://www.virtualschool.edu/cox/ - what a hero ! Esse ? o cara !

----

Apples ObjC docs at [http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html]

Apple recently created a mailing list for discussing Objective-C related issues: [http://www.lists.apple.com/mailman/listinfo/objc-language]

----

Effective Use of the Objective-C Java Bridge Within Project Builder/Xcode: XcodeObjCJavaBridge -- AlainODea

----

I've added a tiny page basically referencing the Apple doc's on Messages, because I forgot that [ ] is the syntax for messages, so I thought someone else might have also. It's at ObjCMessages.

----

Anyone learning Objective-C may like to check out this archived, freely available NeXT manual:

http://www.toodarkpark.org/computers/objc/objctoc.html

Full of fascinating insight into the basics that are often left undiscussed in the Cocoa stuff. 
It is a good introductory text, from a simpler era, but still surprisingly relevant. - AlexClarke

*what the hell is wrong with that page? the linked docs are in like a 48 point font*

----

The manual above from the NeXT days is just the older version of the apple doc cited further above !
If you prefer PDF over html, check out"

http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/ObjC.pdf

Try to compare the first few chapters between the new and the old ... it is almost the same, including the sample code snippet.
It's good to know we now have the old NeXTStep and the old Mac OS in one and the same OS, the ultimate OS X !

----

Objective-C is a pure superset of C. But C++ is not a true superset of C. (This seems to be a common misconception.) The differences between C and C++ can lead to all sorts of subtle or annoying problems. One of the archetypical examples of these differences/problems is given by this example:

In C, character literals such as 'a' have type int, and thus sizeof('a') is equal to sizeof(int).
In C++, character literals have type char, and thus sizeof('a') is equal to sizeof(char).

There are many other examples, from C++ being more strict about implicit casting to issues of linkage. Some IDE/compilers (not necessarily Xcode/gcc) go as far as to compile all C code as C++ by default to hide these problems from developers who might not be aware of these differences. But this has drawbacks, particularly when trying to distribute binary versions of libraries as the C++ Application Binary Interface (ABI) hasn't been particularly standardized or stable as opposed to the C ABI. (gcc 4.0 hopes to stabilize the C++ ABI for awhile within certain constraints. Apple posts a document here about it: 
http://developer.apple.com/documentation/DeveloperTools/Conceptual/CppRuntimeEnv/Articles/CPPROverview.html)

There also seems to be concern with C and C++ continuing to diverge, particularly with C99 and the proposals for C++0x.

Bjarne Stroustrup has written a paper about the differences between C and C++:
http://public.research.att.com/~bs/sibling_rivalry.pdf

For a list of current incompatibilities between ISO C and ISO C++:
http://david.tribble.com/text/cdiffs.htm

Back to Objective-C, being a true superset of C avoids all of these problems which is a very nice benefit of the language. However, I am still unclear what (if anything) C99 means to Objective-C (gcc does not yet invoke C99 by default). 

Objective-C++ however brings in all the baggage associated with C++. That being said, Obj-C++ can be a very handy tool, and is used by many shipping products for OS X, but it is worth understanding the impact/tradeoffs of using C++.  --E. Wing

----

Leopard will introduce significant updates to Objective-C, referred to officially as "Objective-C 2.0", and unofficially as C*. The following information is gleaned from public sources: the compiler source code (http://gcc.gnu.org/viewcvs/branches/apple/trunk/gcc/objc/) and compiler test cases (http://gcc.gnu.org/viewcvs/branches/apple/trunk/gcc/testsuite/objc.dg/). I had not have access to privileged information at the time of writing. --JensAyton
Kudos to Dietmar Planitzer and Dave Vasilevsky for doing a lot of the digging.

Interestingly, the compiler source code says "This module implements the Objective-C 4.0 language", rather than 2.0.

Known new features - which will all be optional to use, at least at first - are:

* Automatic GarbageCollection.
* A new iterator construction:     for (id element in someArray) { [element doStuff]; } 
*     @required and     @optional keywords for FormalProtocols.
* Weak references, indirect object references which don't stop objects from being garbage-collected.
* GCC attributes on methods.
* Properties. A property is an ivar with a getter and setter, unless it is read-only. There are several ways of implementing them. In the simplest form, the ivar, getter and setter are generated by the compiler:     @property NSString *name;. An existing ivar may be specified:     @property (ivar = _tag) int tag;. You can also specify your own getter and setter:     @property (getter = getFrame, setter = setFrame:) NSRect frame;. Properties can also have two properties: readonly, meaning there is no setter, and copying, meaning the value passed to the setter will be copied. Client code uses properties by using dot notation on the object pointer:     NSWindow *myWindow = getAWindow(); NSLog(myWindow.name); myWindow.frame = someRect;.
* Warnings will be generated for use of     @defs and implicit typecasts of SELs, saying these "will not be supported in future."


----
more Objective-C 2.0 hints from http://lists.apple.com/archives/Objc-language/2006/Aug/msg00039.html:


- We have the syntax for properties, in both classes and protocols:

    
 @interface MyClass : NSObject
 @property int foo;
 @property(getter=_getBaz,setter=_setBaz) float baz;
 @end
 
 @implementation MyClass
 - (int)_getBaz { return baz; }
 - (void)_setBaz: (float)newBaz { baz = abs(newBaz); }
 @end
 
 @protocol MyProtocol
 @property(copies,readonly) NSString *bar;
 @end
 
 int main(void) {
 	MyClass *m = [MyClass new];
 	m.baz = -5.0;
 	printf("%f\n", m.baz); /* -> 5.0000 */
 }


*I notice you're using the property call on m to set the "baz" property to a number.  Does that mean the number is getting AutoBoxed?*

- Protocols can list required or optional methods:

    
 @protocol MyProtocol
 @optional
 	- (int)foo;
 @required
 	- (void)bar: (int)arg;
 @end


- The syntax for foreach is pretty obvious, but here it is anyhow:

    
 NSArray *arr = randomListOfStrings();
 for (NSString *s in arr) {
   printf("%@\n", s);
 }


It looks like there may be a facility for enumeration of user- defined types...

- To accomodate GC, fields can be weak:

    
 @interface MyClass
 {
 	__weak id some_var;
 }
 @end


----

Do I need to protect my Cocoa app's ObjC methods from being called by other apps or processes, and if so how?  My app will surely malfunction badly if that happens!   Thanks --DarelRex@gmail.com

----
No.
----
Thanks for the quick answer...  But I assume there is a way to make your own app's methods accessible to other apps/processes, right?

----
Yes, it's called DistributedObjects (accessed via NSConnection), and it doesn't happen unless you explicitly set it up.

----

The article mentions that it's known "informally as C*"; that's a bad name.  C* is the name of a data-parallel extension of C that has been around since the 1980's.  See here: http://en.wikipedia.org/wiki/C%2A

----

Do synthesized properties need to be looked at at all in -dealloc, or does ObjC release them automatically when an object deallocates?

----
The synthesized part of the property is only the accessor code itself.  This does not need to be allocated or deallocated.  The storage used for the data, however does need to be deallocated just as if you had not used @synthesize.  This is unrelated to the properties themselves, however, since properties does not remove the burden of writing that code anyway.

To be clear... here's some code with properties:

    
 @interface Foo : NSObject
 {
     Bar *someMember;
 }
 @property (retain) Bar *someMember; // here we're telling the property system to retain this method when it's sent to us... this could be "assign" or "copy" or all kindsa stuff
 -(id)init;
 -(void)dealloc;
 @end
 
 @implementation Foo
 @synthesize someMember;
 -(id)init
 {
    if(![super init])
        return nil;
 
    [self setSomeMember:[Bar bar]]; // allocation of the data for "someMember"
    return self;
 }
 
 -(void)dealloc
 {
    [self setSomeMember:nil]; // freeing the data in "someMember"
    [super dealloc];
 }
 
 @end


If, let's say... you didn't want to allocate the memory in your init method... you'd still want to dealloc it in your dealloc, assuming your property is retaining or copying it, which the default property mechanism does.  If your property is NOT configured to retain or copy it (using the "assign" mechanism perhaps) then you might or might not need to dealloc it depending on your application.  Wondering if this article should be refactored and this stuff put under a properties document article.  -- JivaDeVoe

----

Pity. I had hoped that all "copy" and "retain" property values would be sent -release or -autorelease automatically at some point during deallocation.

