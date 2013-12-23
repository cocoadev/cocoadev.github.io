---
layout: page
title: CSharpVersusCocoaDiscussion
---

**Objective-C 2.0 now has garbage collection**

C# requires so much less code to write then Objective-C, it's ridiculous. Makes Objective-C/Cocoa seem old and outdated. But I doubt it'll ever happen anytime soon.

----

Can you give some examples of cases where you're finding Objective-C doesn't offer you the same economy of expression you're finding with C#?

----
Well...C# doesn't require headers for any classes - only the implementation file, and if you have multiple classes spanning multiple files, you only need to put them all within the same namespace, and then no "#import" or whatever. Also, there are no pointers at all in C#. And Visual Studio has syntax-aware spell checking. I'm new to C# but just from the beginning it's easier in certain ways. I'm not saying it's better though :) I still love Objective-C/Cocoa.

----

There *are* some good technical reasons about why ObjC-GC couldn't be perfect without it becoming a very different beast.

From my standpoint, I would love GC in Cocoa. But I also want a lot of other things-- many of which are being provided-- like not having to write accessors (god I hate them), and not having to write controllers, and not having to write model classes...

And suddenly CocoaBindings and CoreData make sense, and one starts to see that yes, GC would *still* be very nice to have-- but is 'needs' appropriate? (It might be! It really might, in this day and age. But as you say, there are ups and downs in all things.)

----

I've been doing some C# coding the last few days, and boy do I miss Objective-C! I thought not having any pointers would be easy, but now that my mind is trained to program efficiently, I miss them! There is a pass by reference in C#, but it's ugly and not very intuitive. The more I use C#, the more I feel like I'm using a hugely bloated language and API (which I'm sure it is). Anyways, anyone else have any takes on C#, especially if you've had actual coding experience with it? (btw I was this page's original poster)

In response to "Namespaces would be nice for ObjC, but header files have one purpose: they allow people to use your code without you giving it away." - you can create DLL files (ooo fun!) very easily, and then anyone can have access to them and use the code within them. I guess Windows DLL's are similar to a .framework except it's all one file (but it's easy to extract files from within it via code).

----

I have to use C# at work, and I agree it has some advantages. GC is certainly very nice, but I find that generally C# code is horrible to look at. For my work it seems to take loads *more* lines of code to do things in C#, even taking GC into account, the code is ugly and full of overly verbose keywords, and it just isn't as free a language as ObjC. There are two things I miss in ObjC that C# has, that is GC and metadata. I miss a lot more of ObjC when programming in C#. C# is inhibiting, overly verbose and bloated, ObjC is enabling, short and to the point, and reads like a dream.

----

By *metadata*, I believe he means introspection, which both C# and Obj-C support. All Obj-C needs to be adapted to the CLR is namespaces. I suggest a @namespace keyword for that.

----

I interpreted 'metadata' to mean what C# calls 'attributes'. It's main use is for introspection, but it offers it in a way that isn't available in Obj-C. It's information that you can 'tag' onto classes, methods, and properties.

An example that comes to the top of my head is the way that N<nowiki/>Unit (http://www.nunit.org) uses attributes:

    
[TestFixture]
class MyTest
{
    [Test]
    public void SomeTest(){ /*...*/ }
}


[T<nowiki/>estFixture] is an attribute associated with M<nowiki/>yTest. In conventional x<nowiki/>Unit frameworks, My<nowiki/>Test *had* to be a subclass of Test<nowiki/>Case. The superclass Test<nowiki/>Case was the metadata that the runtime used to identify test fixtures in order to run them. In N<nowiki/>Unit, Test fixtures are free to inherit from anything they want. They just need a T<nowiki/>estFixture attribute.

Similarly, [Test] is an attribute associated with S<nowiki/>omeTest. x<nowiki/>Unit normally requires that you begin your method name with 'test', such as 't<nowiki/>estMomBakesApplePies'. In N<nowiki/>Unit, implementors are free to use whatever naming convention they want. Just tag the test with the [Test] attribute.

Obviously you can declare your own attributes. They are classes, and have a base class they must inherit from (ironically, they aren't identified by a special attribute themselves). The attribute declaration above is an instantiation of an instance of the attribute that takes place at *COMPILE* time. It's always around, and always associated with the class/method/property you've defined.

It doesn't seem like a huge deal, and it isn't unless you're writing or using code that does a fair bit of introspection. Metadata allows another dimension of use for introspection that gives you much more flexibility. You no longer have to use naming or inheritance conventions. Sometimes those things are appropriate - but sometimes they are too limiting. Naming conventions can get big - and implementors can accidentally create a method that isn't *supposed* to conform, but does. Or the peculiarities of English can make the naming convention give you methods that really don't read well.

Also - more than one attribute can be assigned in C#, allowing you to give a method several roles.

-- TimHart

----

Strange to see an ObjC -- C# comparison. C#--Java seems more of an Apples to Apples comparison as it were. I guess I just don't see any great value to garbage collection or namespaces, but that's probably why I'm indifferent to Java as well.

And for that matter what did header files ever do to anyone? Show them some love.

----

I will agree on the garbage collection. The C/C++/Obj-C languages were never meant to be a high-level, hold your hand, type of languages at all. Although, they are probably the right choice for the implementation of such a feature, of another language, *Cough* Java *Cough*. I would like to see namespaces in Obj-C though.

----
Well, this conversation is moot.  Check out the following: http://developer.apple.com/documentation/DeveloperTools/gcc-3.3/gcc/Garbage-Collection.html
We finally got optional gc!!!!

*Take a look at the GarbageCollectionDiscussion, and you'll see that that document doesn't apply to the Apple/NeXT runtime. False alarm everyone.*

I don't know why so many people seem so hung up on this "cocoa doesn't have garbage collection" meme. Learn the memory rules! They are trivially simple! Once you "get it" (takes about ten minutes to read up on, maybe half an hour of coding to understand it properly) then you rarely run into trouble with it. Most people seem to get into trouble because they try to do too much memory management - most of the time you can do nothing and that's the right thing to do. You only have to keep the rules in mind at a low level when you're coding - just a vague awareness is all that's needed, not a constant vigil about when to release or not, etc. Learn the rules! Anyone who thinks they are too complicated probably has "overstood" it, if you know what I mean.

----

The argument that C# is better "because it lacks headers" doesn't sound like a plus to me. How do you distribute the public bits of your code while keeping the implementation private? How do you optimise the compile process by precompiling just the headers without having to parse the entire implementation? While headers being separate goes back to the dinosaurs, so maybe there is a case of just being used to it, it is done for good reason. Personally I'm not sure I'd be comfortable in an environment where they didn't exist. That said, I'm not familiar with C# so maybe the way it does this is cleverer than it sounds.

----

You use a DLL if you want to keep the implementation private. But everyone knows that .NET is so easy to decompile. Google search ".NET Reflector"

----

OK, so how do I publish the API to my private DLL so others can link to it? Surely I need.... a header!!

----
Nope, no need. I don't want to argue how .NET DLLs work here. They're completely different than Win32 DLLs. Go search Google to figure it out. But the point is, there aren't .h files in the C# language. Trust me :p

----
Even if I didn't have to use headers, I still would.  The small extra time investment it takes to maintain them is more than balanced out by the amount of time they save me when the time comes to use a class.  They're short enough that they provide a great place to write class documentation so that I can look up my notes on some method I wrote last year without having to scroll through a 700 SLOC implementation file.  They're also a good place to take a quick look to see what methods a class implements without having to scroll through said pile of implementation code.  And it really doesn't take any extra time to keep the .h and .m files synchronized, thanks to the wonders of copy and paste. 

----

I have to agree with you. Headers are ideal places to document your stuff and many tools such as HeaderDoc expect you to. If  want to use someone's class the headers are the obvious first thing to look at. One thing I'd like to see in Xcode though is automatic expansion of a header that you've written for a class to a skeleton .m file. That part gets tedious especially when building a biggish class in one go rather than piecemeal (since -ahem- some of us occasionally do rough out stuff on paper before we start to code ;-)

----

You header defenders are ridiculous, there is no extra information in header that couldn't be read from an implementation file if the compiler was modified to do so. Header doc style method documentation is best served with the implementation, why would you want to look in another file? If you want to publish header information to a private api use or if you don't want to scroll through lines of source (and you don't have code collasping) a header doc like system to keep documentation up to date by running a program seems a bit better than copy and paste. Plus if you are actually trying to reuse code and actually are using inheritance your headers are going to be as easy to look up documenation rather than header doc. You know that you can generate headers information for any dot.net dll or objective-c framework from eithers compiled binary thanks to introspection in both languages, the only difference is you miss some of the c bits in the objective c header because objective-c doesn't have complete introspection so don't kid yourself that objective-c has awesome introspection.  Objective-C headers aren't very useful in themselves they are only necessary because with objective-c you are still only working with C.

----

Sometimes you don't want to read a whole book just to get a rough idea of what it's about. Same with code. The point is not that I COULD generate headers from an implementation, it's that sometimes you really don't want to, especially if it means that user has to run it through some other process I have no control over. Far easier to supply a public document that saves them all the trouble. Still you're entitled to your opinion, even if for many situations it just isn't appropriate. 

----
I am an Objective-C fan.  I do most of my daily coding in C++, but I am always overjoyed to get back to Objective-C.  I have never been a big supporter of Java mainly because my areas of interest are real-time embedded systems and desktop applications.  In my experience, Java is not well suited to my areas of interest.

C# is very compelling on paper, and .Net is a huge improvement over previous Microsoft abominations.  I would say the only framework I dislike more than MFC/Win32 is Apple's Carbon/QuickTime.  I think a final opinion on C# is still premature for me though.

I have no strong opinion about header files.  I use them all of the time with Unix and Unix like systems, C/C++/Objective-C.  I also sometimes use Ada Package Specifications which are a kind of header file.  I use other languages like Python and Ruby occasionally, and I don't miss the headers.  I think Eiffel has a nice system, but the language feels like a straight jacket to me, so I don't use it anymore.

Then, there is the ultimate header less system, Smalltalk!  Given that Objective-C is Smalltalk mixed with C, I think the header philosophy could reasonably come from either of the ancestors, but the advantage of seamlessly re-using the billions of lines of C code including headers makes any small inconvenience of maintaining headers insignificant.

-EMB

----
* 
Sometimes you don't want to read a whole book just to get a rough idea of what it's about. Same with code. The point is not that I COULD generate headers from an implementation, it's that sometimes you really don't want to, especially if it means that user has to run it through some other process I have no control over.
*
If you can generate all the information for the header file, then you can generate all the information for header doc, thus you can produce a public document that is much better than a header, thus this isn't a good reason why you don't want to. People work really well with java, ruby, python, c# all without headers, there's no need to be afraid, it's really better without them.

