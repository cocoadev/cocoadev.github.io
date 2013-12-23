---
layout: page
title: JavaVersusObjectiveC
---



There have been many times when I have debated over whether to use Java (JaVA) or Objective-C (ObjC) to write a Cocoa program.  This page is here to list and discuss the pros and cons of using each language with the Cocoa frameworks.

Pros of Java:

* Platform independent (write once, run anywhere)
* Automatic GarbageCollection
* Consistency of syntax.
* Availability of many third-party libraries.
* No header files or function prototypes
* Direct access to member variables


Cons of Java:

* Performance cost of the bridge. JavaBridge XcodeObjCJavaBridge
* Performance cost of the VM.
* Some Cocoa APIs not available.
* Makes a cross platform language grounded to an OS. 
* Uses lots and lots of memory


Pros of Objective-C:

* Sweet.
* Excellent runtime dynamism.
* Categories.
* Use standard C code natively. 


Cons of Objective-C:

* Explicit retain-release MemoryManagement
* Not as many resources available.
* Even more proprietary, unless you count GNUStep.
* Some believe ObjC (or Apple's ObjCRuntime in particular) is slow (though Java probably isn't much faster, with Cocoa bridging, and the virtual machine, and such; plus, Apple's optimized their runtime highly). Note: Cocoa-Java is slower than Cocoa-ObjC - whether Pure Java is faster than the latter is a different issue.



----


Criticism leveled at Java Cocoa programming:

-- Java is too slow

  This is a real consideration if you're writing a full screen immersive game, such as a first person shooter.  Both Java and Objective-C trade speed for flexibility (eg: shorter development times) and unless you're writing such a game, your code's performance is going to depend more on your practices than on the environment you choose.

-- Java doesn't have the level of support of C/C++

More of a glass-half-empty, glass-half-full kind of perspective.

-- Java is a PITA when it comes to typing, etc.

  This is true, and this gives Java development a %5 penalty in speed, in my opinion.  Even with reference counting, Objective-C has the edge in functionality completed in a given amount of time.

-- Java doesn't support using selectors.

No longer true.  NSSelector works just fine, and allows you to structure your Java code much like you would if you were writing in objective-C.

-- Its easier to call Java classes from Objective-C than the other way around.

This is true, so if you're an Objective-C programmer and want to make use of the nice Java code that's out  there, you can do so with little hassle.


----

Well I cant talk numbers because I'm still pretty new to this, but I know the few apps that I use that I know were written in Java do run very noticeably slower.  Enough to be irritating anyway.  I think its cool that Apple provides the option and if you're developing for multiple platforms... If it's an OSX only app, I'd rather not have it written in Java (from an end user point of view that is).

CliffPruitt

----

Comparing the languages Java and ObjC, rather than the runtimes Java and ObjC, there are a few things to add in favor of Java: 

Java is a well designed and more complete OO language, it has real access-modifiers (you know public, protected, private) whereas ObjC has some markers that don't really do much. In Java you can apply these modifiers to methods as well, in ObjC you have to resort to ugly hacks with private headers and categories to do the same, and even then the methods are not truly private or protected. Furthermore Java has markers to declare classes and methods abstract - in ObjC you can't do this in any nice way. Java also supports inner classes, both anonymous and named.

Don't think that I don't like ObjC, but one feel very limited programming in it, just something like the singleton pattern, or the lack of abstract classes... but comparing to Java might not be fair, ObjC still beats the hell out of C++. **Ouch you hurt my feelings! But really, even Brad Cox admitted that Obj-C and C++ fullfill different roles, and can even complement each other.**

-- TheoHultberg/Iconara

----

As I see it, ObjC appears more dynamic than JAVA -- for example I can add a category to an existing class (now don't you wish you could extend the Graphics object given to your paint()-method in JAVA?) and for example you can have your 'copy' menu item send a copy-message to the first object of the responder chain which implement this message, you can tell the system that you have implemented certain methods but really just forward them to perhaps the "active" object in an array or similar. Highly desired features which I lack in JAVA.

Furthermore ObjC++ also includes the full range of the latest ISO C++. For example when you work with OpenGL you can create your own Vector type, use operator overloading so that you can add, subtract and perform other operations, furthermore you can implement type-conversion methods so that a Vector can (implicitly) be converted to a double* (which the GL functions understand) and likewise automatically have a Vector constructed from a double* (perhaps a result from some GLUT function) -- this saves a lot of typing (well, JDK 1.5 has auto boxing, I don't know if it is as general).

Another thing is templates -- after learning STL they really are a lifesaver, as an example take this function:

    

template <typename _Iter> void print (_Iter first, _Iter last)
{
   for(; first != last; ++first)
      cout << *first;
}



It will output all the elements between first and last to the standard out, this works for a normal ANSI C array, an STL vector, an STL list, an STL map or your own containers (if it has iterators with overloaded operator* (to get the element) and operator++ (to advance one).

You would be able to do the same in JAVA using an interface, but there would be more typing overhead, you'd need everyone to know about your interface, it wouldn't work for primitive types and of course, it would be much less efficient.

But I am only scratching the surface of what C++ allows you to do...

Seriously, for me there is no doubt: C++ is the most powerful language except for "dynamic" (run time) features, but here I'd take ObjC over JAVA any day... and with ObjC++, there is no question!

For the records: there are languages I prefer over C++, but often these are not really up to the job of real life programming, for example load a file and decode from MacRoman to UniCode or other "low level" stuff that goes on in real programs...

%----%

In parts Java, C++ and objective-c cannot even be compared - the first being strongly typed and the latter weakly typed (i.e. you can send a message to an object even if you don't know its type). There is a big debate going on right now wether weak typing is more productive that having everything enforced by the compiler (e.g. Bruce Eckel http://mindview.net/WebLog/log-0025 )

As for language features, Categories are very useful, how often have you needed just a slight extension to existing classes ... this IMHO reduces the complexity of the code a lot

Just my 2cents, HaRald


----

You say that categories are a language feature, but wouldn't you agree that the Cocoa API was designed with this feature in mind? For example you cannot sub class NSDictionary or similar class clusters, but you may often need to extend their functionality, so here categories are the only solution.

With respect to speed, not only does JAVA applications rely on the Cocoa APIs, but it would seem that dynamic dispatch in Cocoa programs written in JAVA is also largely handled by the Objective C run-time, through NSSelector, which is required to support the 'first responder' idiom and other features which require a selector (method) to be a type of its own (not declared in an interface or super class).

I guess forwarding is then also implemented through NSSelector -- though browsing through the JAVA reference documentation sure lack a lot of methods which are defined for the Objective C version. Is the documentation just out of date? or does for example the JAVA version of NSResponder lack all the action methods and the JAVA version of NSObject lack all the performSelector:afterDelay: and friends?

----

Concerning Cocoa Java, I wouldn't recommend it.  To me it's getting the worst of both worlds: a slow program that's not cross platform.  I think it's worth the effort to learn Objective-C if you are going to write a Mac-only program.  Plus, a hidden benefit of objective-C is that you not only get all the pre-existing C and Objective-C code out there, you get the multitude of C++ libraries as well.  Using Objective-C++ you can really use some good code that's available.  So there isn't much argument in saying that Java is better do to the code that's out there.  I happen to like worrying about garbage collection myself too, but that's just my take on it.  

I like that I can mix C++ and Objective-C a lot.  To me C++ still has some really good features that other languages are missing.  Templates are great.  The things you can do with them never cease to amaze me.  I also like multiple inheritance a lot.  Even if you don't use it often, it can be a real life savor sometimes.

Another thing I really like is being able to go directly into the Objective-C runtime.  If you take a look a the book Cocoa Programming there are really some amazing things you can do since the runtime is all written in C and available to you.  You can do some great optimizations and it's a generally good learning experience.

I don't know, I still haven't fallen into that "computers are so fast today you don't need to worry about good code anymore" mindset, so I tend to stick to Objective-C and Objective-C++.

- FranciscoTolmasky

----

I too would have to disagree with people talking about the speed issue. I haven't written any Cocoa Java apps that did particularly processor intensive things, but the apps that I have written in Cocoa Java run perfectly smoothly. If you are doing something that requires a lot of processor time, obviously you should use Objective C, since you are just working on a somewhat lower level.

One advantage of Java that hasn't really been emphasized here is its traditional format. I took a long time to get into Cocoa programming, because I balked at the Objective C syntax, what with its bracket function form. It was just too confusing for me. I'm not saying that Objective C is a bad language, or that its syntax is bad because it is so initially confusing, but Java can really let you get your foot in the door if you don't know Objective C. Since programming in Cocoa, I have done some things in Objective C. I'm not completely comfortable with it yet, but until I am, Java is a perfectly viable option for Cocoa programming.

I like that "There is no One True Language For All Programmers" thing. We should abbreviate it to TINOTLFAP and pronounce it like Tin-oatle-fap. We must work to get it into the world lexicon! Sort of like TINSTAFL in economics.  ^_^

----

Rather, "there is no one true language for all situations". OO-Perl is generally a bad idea, and Fortran and pattern matching is not optimal. -- TheoHultberg/Iconara

----

I can personally testify that there is no substantial speed decrease when using the Java Cocoa APIs vs. Objective-C.  Certainly not enough to sacrifice the cross-platformability (if you need it, that is). My company needed a solution that was both cross-platform and looked pretty.  I opted to use Cocoa-Java on OS X, and the Eclipse platform on Win32 and Linux. All of my core model classes are written in pure Java and make calls to Controller objects that conform to interfaces which interact with native GUI views.  It's worked wonderfully so far and is surprisingly maintainable. 

----

It's generally silly to talk about speed when writing desktop applications. Most applications would run perfectly smooth, even if written in Python (there are proofs to that: FontLab and BitTorrent) or Ruby, or AppleScript. Since the applications always have to wait for events from the user, they spend most of their time doing no-ops anyway. 

--TheoHultberg/Iconara

----

I totally agree. Unless your target user group is running G3s with 1 MB of RAM (Is that even possible any more?) speed optimization for desktop applications is pointless, unless you are eliminating some bottleneck that makes your application unusable. What I mean is, don't waste your time optimizing something so that your users have a 0.00012 second productivity increase that no one will know about anyway, but perhaps optimize something that prohibits your program from running. Memory leaks are a VERY good example of what you SHOULD fix. "Fixing" a program by using a different "faster" language? N-O.
It all comes down to preference. I personally hate working with JaVa, but I love ObjC. 
However, if you are writing anything like a rendering engine or some computation cluster thing, C and ASM and *possibly* C++ are the way to go. 

Summary: If speed is an issue, go back to C. Do not pass C++. Do not collect Object-Oriented. -RossLeonardy
Edit: Maybe we should find a way to integrate SETI into our applications so they don't waste cycles. Or Folding@HOME. Or ElectricSheep.
----

I personally like Java, but learned Objective-C (under duress, at the time) because a few classes just don't work on the Cocoa Java side. For pure applications programming (databases, terminal apps, multi-computer compatibility, etc.) I believe Java is a better language. But that's just me. For Cocoa, although I can't even identify a speed difference, Objective-C is a bit more reliable. But being a Java guy, I found the privacy modifiers (e.g. @protected) confusing and almost irrelevant, considering you almost never use anything but @private (the default). You also have to mix OOP with normal (instruction-based) C syntax (e.g., for the equivalent of static variables, constants, and the like). Also, counting "Makes a cross platform language grounded to an OS" as a con is not valid. Cocoa ObjC is already grounded to an OS, so it's not like it's an advantage. And about templates -- well, Java 5 (1.5?) has generics, which function more reliably, if not familiarly. Let's hope ("us" being Cocoa Java programmers) that 10.4/Tiger will have rewritten Cocoa Java APIs.

Here's a good quote I composed on the spot: "Thou shalt not deny the worth of a foreign (programming) language. All must obey, for I am root." (EDIT: I think I've heard something like it before though...at least the root part.)

TOLERANCE, people! -- JediKnil

*@protected is actually the default, much like package protected is default in Java.*

----

I am new to the mac and finding that objective-c can control openGL windows and that java cannot under cocoa (easily - there are some daunting hacks).  under cocoa it is also not clear how to make a Java2D window or anything the like - is maca java graphics limited to swing and companions?  if so i would counting 'lacking graphic support' as a big minus for java/cocoa and 'having graphic support' a plus for obj/cocoa - 

if i am incorrect in my impression, please correct me.... i am learning objc solely to do things that i would *love* to do in java.  although objc seems beautiful so far 

*Most (if not all) of the AppKit classes are available in Java/Cocoa. And you could theoretically create Swing windows from ObjectiveC, if you would ever want to, so yes, you have got it wrong =). Read up on JavaBridge and you'll understand more.*

----

For those who don't know where Objective C gets its syntax from, it's Smalltalk.  I have personally always preferred Smalltalk-like syntax to C++-like syntax.  Perhaps this is why I got along well with Objective C.  I find it incredibly easy to read..

----

Comment: "Platform independent (write once, run anywhere)"
I'm pretty sure you said "to write _cocoa_ programs". So that pro is out

p.s. I'm an anti-java person
Aptiva/Fj�lnir �sgeirsson - http://fjolnir.zapto.org

*Yeah, well, in that case I think that "Sweet" is probably out too.*

Comment: "Direct access to member variables"
I'm not sure this is a plus in any language. Besides, the access semantics are the same in obj-c vs java. @Public, @Protected, @Private are the same as public private and protected. Maybe the differing defaults confuse you. None the less, you should not access another instances instance variables directly. Jon H.

