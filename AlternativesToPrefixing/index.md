---
layout: page
title: AlternativesToPrefixing
---

While 2-letter prefixes were a good idea in the late eighties, nowadays there's just not enough flexibility there.

There are those who suggest 4-letter suffixes for their library prefixes.

OpenGL uses 2-letter ('gl'), 3-letter ('glu'), and 4-letter prefixes ('glut') as enumerated in the BlueBook.

Apple's ScreenSaver framework has an 11-letter prefix for all of its public symbols: "ScreenSaver". 

Some folks recommend deriving symbol prefixes from reverse domain naming conventions, to further reduce collision risk: "ComMacSteve". Another advantage of using this technique is: no registration page required. There are downsides, of course.

Hmmm ... I can't quite come up with a clever conclusion to this note. Meditate on this for a while.

-- MikeTrent

----

A solution is to use really long names, and then when you don't want to use the really long prefix, to #define a shortcut... when your done with the shortcut, #undef it.  This is similar to the concept of some _other_ languages using the "namespace" and "using" kind of words.

----

Up until now, I've been ignoring prefixing and namespaces in Cocoa (except for Java packages, which I like). But now I'm in the process of writing an application that accesses class information defined by the user. This creates a possible collision. So my current solution (until if-when Apple adds namespace/package support to ObjectiveC) is to use the following code:
    
 #define MyClass ComMyWebsitePackageName_MyClass
 ...
 @interface MyClass ...

My only immediate problem is that I have to go change it manually in Interface Builder, and this doesn't solve anything if I accidentally include someone else's (or even my own) header that already has a     #define MyClass. But for now I think it's better than nothing, and easier than writing out the full name all the time. This could also be used with prefixing, so that you would abbreviate your package name to a prefix. This would reduce #define collisions as well.
-- JediKnil

----

I like Knil's scheme.  One could also consider putting the #define's in a prefix header that also imported the headers files.  And there's no need to do this in application code (only frameworks and bundles), so long as all code agrees that the no-prefix namespace is reserved to an application's main bundle.

Anyone else have thoughts?

----

    
 @compatibility_alias MyClass NetIconaraStupidLibraryMyClass;


Hey ho,     MyClass and NetIconaraStupidLibraryMyClass are now two names for the same class. More ObjC than #defines, but can't undef it though.  --TheoHultberg

*I moved Theo's comment from the bottom of the page to here - because I believe it's extremely relevant and deserves a bit more visibility. A link on RedHat's site (below) claims that this keyword was "completely forgotten about"*

http://docs.redhat.com/docs/en-US/Red_Hat_Enterprise_Linux/4/html/Using_the_GNU_Compiler_Collection/compatibility_alias.html
http://developer.apple.com/library/mac/#documentation/DeveloperTools/gcc-4.0.1/gcc/compatibility_005falias.html (basically the same as above).

----

This is how to use the directive     @compatibility_alias as an import directive, like in Java:

    
 #import "NetIconaraStupidLibraryMyClass.h" // defines the class by the same name
 
 @compatibility_alias MyClass NetIconaraStupidLibraryMyClass;
 
 // now I can refer to MyClass instead of NetIconaraStupidLibraryMyClass, namespace problem sorted.


Although I dislike this idea on aestethical grounds, it works quite like proper namespaces. If you want to "import" a whole library, you can prepare a header file with all the     @compatibility_alias directives, and import that header. --TheoHultberg

----

I believe this to be a problem that Apple should fix; we the developers are unfortunately going to run into collisions if left to our own devices. I personally have classes I wrote on my hard drive which include prefixes such as GL, LS, FD, FDGL, Meta, Emily, CBG, Ce, EK, Locate, and q. Now, only one of those (LS) is likely to ever get exposed API, which is why I didn't put any of the rest in the list above, but you can see the problem quite clearly.

The problem is pretty simple:

* We the developers need good namespaces to avoid collisions, but
* We the developers don't want to write out ComMyDomainCollectionMap a hundred thousand times.

Beyond being very silly, that is very, very unreadable. So,

* We the developers might like a solution intrinsic to the language. By "might like" I mean "probably need." Or maybe even "must have."


Java's com.domain.packaging.thingy seems to work pretty well. Anybody think it could be adapted (without the above ComMyDomainCollectionMap silliness) to ObjC?

-- RobRix

----

I would like to see something like the following.

    
 @namespace com.mycompany.mynamespace
 .
 .
 .
 @end


where the namespace hierarchy can be any reasonable number of levels deep.

Referencing fully qualified objects could be something like...

    
 com.mycompany.mynamespace.MyObject *p = [com.mycompany.mynamespace.MyObject 
            objectWithSomeParameter:foo];


**Yuk**, So something like the following becomes very popular...

    
 @using com.mycompany.mynamespace;


**
How would you propose dealing with code that uses objects from multiple name spaces? Namely using any Cocoa class with your name space.

And if those two namespaces both have classes named CoolWidget. *(Extreme I know.)* --TimothyHatcher
**

which can specify a namespace name or a fully qualified type name. This could appear either at global (compilation unit), class, or method scope.

The runtime could default pre-namespace compiled code, or code not using the feature, to the global namespace as it essentially does now.

The remainder of the namespace rules (lookup and whatnot) could be stolen from Java or C++; as they both are fairly mature and work well. I would suggest C++ semantics as Objective-C++ already exists and it would cut down on surprises for C++ developers if they worked similarly...

----

PS you can do something like javas com.packaging.thingy already in cocoa: 

*Define a class which represents your package.
*That class defines *class methods that return a class*.
*these classes can also represent packages in the same way.
*import the top level class (package).
*If you are adding a class to a package define a category.

then you do: 

    [Cocoa com] packaging] thingyClass]

to get your class. Not too hard methinks, doesn't involve any language extensions. Don't know why nobody does this already. :-) 
Perhaps even using CCD is unnecessary. -- [[MikeAmy

----

Although this is a very creative solution, I believe it suffers from a serious flaw in usability: it looks like messaging, but has an entirely different meaning. It will be too distracting/confusing in actual usage. It needs to look different at a glance.

It also has s second serious flaw: it has no point. The point behind namespaces is to refer to a unique global name by an abbreviated local name. If you always have to refer to something by the full name, you might as well make the class names longer, e.g. ComDustinVossList.

It'd be nice if
    
 Class List = [ComDustinVossList class];
 List *aList = List alloc] init];
 worked, but it doesn't.

No, the real solution is language-level namespaces. I'm sure the [[ObjectiveC language nebbishes are already hard at work. When they've get it specced out, it can go in GCC.

--DustinVoss

*Err, that doesn't work? I've written code which messages Class variables and ivars... why would this particular case not work?*

I can imagine that the     [List alloc] part would work, but not the     List *aList declaration -- unless one created a typedef, I suppose. I haven't tried either part, though, my statement was more of an educated guess.

Under further consideration, I wonder if one could write a macro to take a global name and replace it with a local name? Maybe something like 

    #define USING (gname, lname) typedef lname gname; static Class lname = [gname class];

although I'm sure I screwed up the syntax somewhere. And this implementation is missing another useful feature of gen-u-ine namespaces: shortened names for a group of classes.

----

Yeah, sorry guys, sometimes I pick up the wrong idea and run with it. Must be something I ate. But maybe think about this a little more. Who are the ObjectiveC nebbishes? Apple? Is anyone working on this? Where is the real discussion if not here?

Using Cocoa classes as an example, NSArray would be     NextStep foundation] array] or maybe just     [NextStep array], [[NSView would be     NextStep responder] view] or     [NextStep view]. - which demands more runtime processing but is also more dynamic - eg you could load a category of [[NextStep and change the class that is returned.
Also you don't have to put up with     [NSArray class] anymore just     [NextStep array]. You'd always get classes in the same way.

But the other way is     NextStep.Array - which is shorter, compile time and demands an extension to the language. Pros and cons to both but I think ObjectiveC benefits from being dynamic - why stop at classes? So I'd think twice before dismissing this idea. -- MikeAmy

----

Ok, I'm making a possibly wrong assumption of designing in terms of protocols rather than interfaces, but:
    
 Class List = [SomeListImplementation class]; // for namespace 
 .
 .
 id <List> aList = List alloc] init]; // we know what it does but not exactly what it is - decoupling


or even:

    
 @protocol Thing
 - (void) doThis;
 - (id) doThat;
 @end
 
 @implementation My : BiggerPackage
 + (Class) array { return [MyArray class] };
 + (Class) dictionary {  return [SomeoneElsesDictionary class]; };
 + (Class) something { return (day == aprilFools) ? [FoolishThing class] : [MyThing class]; };
 @end
 .
 .
 <Thing> wotsit = [[[My something] alloc] init];
 [wotsit doThis]; 
 [wotsit doThat];
 .
 .


There is no List interface, only a protocol, and implementations.
To me this seems nice from a design point of view, what do others think? -- [[MikeAmy

----

The protocol idea is fine from a "does this work?" point of view, but it doesn't help the namespace problem. 

*So how will javas.com.packaging.thingy do any better? It seems to me it is just six of one and half a dozen of the other. With java style packaging, abbreviated class identifiers in the code refer to globally unique class names. These names are specified in the #import statements at the source-code level. The actual classes can still be referenced using their full name, so whether the full name is javaComPackagingThingy or java.com.packaging.thingy doesn't really matter. 

But say the classes were referenced in mutable global variables or using class methods like above? You get your abbreviated name, you get runtime control (better than the static control with just #import) over what that local name refers to, and you can still refer to a concrete class using the full name. How is this not a solution to the namespace problem?

If you're worried about runtime-imported classes overriding your classes, you can a) check the bundle you are importing for namespace conflicts. If there are conflicts, then load the class anonymously (classes don't have to be registered) and hide behind a method like above. b) make your class names really long. After all, imported classes could still use your namespace if they really wanted to be malicious. But if you expect malicious code, your guard should be up anyway. So I don't see how this isn't a solution to the namespace problem. And how does java.com.packaging thingy sort out using identically named classes from multiple namespaces? With this way you get a chance.*

You've just moved from having to make sure your classes have unique names to having to make sure your protocols have unique names.

*Yep, easier innit?*

Ha ha ha! Now explain how I can "check the bundle you are importing for namespace conflicts" (hmm ... solve all three conflict spaces: classes, protocols, and class/category pairs) without actually loading the bundle (and conflicting) and you'll win a special prize. -- MikeTrent

Ulp - I lose!? The only ways I could think of were 
*using: 
    CFBundleGetDataPointerForName([bundleToTest _cfBundle],className);
to  test existence of each class, which might not even work on a yet-to-be-loaded CFBundle and gives: 

    *** Uncaught exception: <NSInvalidArgumentException> *** -[NSCFType absoluteURL]: selector not recognized for loaded bundles. Stab in the dark.

*Parse the bundle headers looking for classes, protocols, categories (which can be done), except this will only let you *prevent* a namespace conflict and depends on headers being there and you having a parser.
*If loaded executable code doesn't get lost, which I suspect it doesn't, then you could initially save all your classes or the classes returned by objc_getClassList(). You could then access your own classes through some class packaging object rather than through the runtime. But what would this interfere with? Certainly any code that uses objc_getClass(char *classname). So you wouldn't use objc_getClass() in your app. And this wouldn't stop bundles interfering with each other unless they knew about it too. I haven't tested this idea yet. Probably dyld will stop it working.

Anything else seems grisly. I guess I don't win the special prize huh? -- MikeAmy

Start a separate process to load the bundle and check it?  --CharlesParnot

----
I haven't done any real tests but I noticed a speed difference between 2-letter prefix and no-prefix back when I didn't understand how to thread properly - the no-prefix version was faster enough to hide a crash. Wonder how big a difference it really makes though.

*Uh, test that again.  There's no way a prefix makes any difference.  If you're talking about race conditions and threading, that stuff is more or less random.  What else the machine is up to at the time of test is going to make a difference.*

I tested that monster a lot. That crash was a pain to get rid of and was consistent. With the prefix it crashed in the same place every time, without the prefix it never crashed again (though it could have if the CPU was taxed - because it was a mask, not a real fix). Anyway...

    
 int main (int argc, const char * argv[]) {
     NSAutoreleasePool * pool = NSAutoreleasePool alloc] init];
 
 		int i=0;
 			for(;i<10000;i++) {
 				MyObject *object = [[MyObject alloc] init]; // ComMyCompanyObject
 
 				[object foo]; // nothing in -foo
 				[object release];
 			}
 
     [pool release];
     return 0;
 }


    
 ComMyCompanyObject;
 real    0m0.374s
 user    0m0.030s
 sys     0m0.040s
 
 MyObject;
 real    0m0.105s
 user    0m0.020s
 sys     0m0.020s


Whether that's something you'd notice in the real world, I dunno. But it is slower with the longer prefix, that much is clear.

*Impossible. The names of the classes are irrelevant to the runtime, as far as I know. They are just symbols. Had the code used somethink like [[NSClassFromString I could have believed it, but otherwise, no way. --TheoHultberg*

----

I can just barely believe the the length of the class name might make a difference for alloc messages, but not for anything else.  There has to be a lookup into a class table for an alloc, otherwise class posing wouldn't work *(Edit: or classnames could just be symbols, sort of like global variables.  Now I get what Theo was saying.. :-)*.  So, if anything you're picking up the cost of hashing a longer string, and that cost would not increase (much) after the classname reached 16 characters (according to Mulle's ObjC hashing article).  Or, maybe you're just getting a hash collision between your prefixed class name and some other class name in your system.  For messages other than alloc the runtime can just follow the isa pointer to get at the class, so I can't see that the length of the class name would make any difference.  And here's my testing result:

Everything clocks in the same to within random fluctuations .

    
 2005-02-10 06:57:33.857 test[1037] MyClass alloc foo release: 0.883287
 2005-02-10 06:57:34.715 test[1037] My18CharacterClass alloc foo release: 0.84174
 2005-02-10 06:57:35.574 test[1037] Com_LongCompanyName_LongClassName alloc foo release: 0.846278
 2005-02-10 06:57:35.632 test[1037] MyClass foo: 0.0440109
 2005-02-10 06:57:35.688 test[1037] My18CharacterClass foo: 0.0422655
 2005-02-10 06:57:35.745 test[1037] Com_LongCompanyName_LongClassName foo: 0.0416702
  

Here's snippets of the code that generated the above:

    
 startTime = mach_absolute_time();
 for (i = 0; i < 1000000; i++)
 {
     obj = MyClass alloc] init];
     [obj foo];
     [obj release];
 }
 stopTime = mach_absolute_time();
 NSLog(@"MyClass alloc foo release: %g", timeScaleSeconds*(stopTime - startTime));


    
 obj = [[MyClass alloc] init];
 startTime = mach_absolute_time();
 for (i = 0; i < 1000000; i++)
 {
     [obj foo];
 }
 stopTime = mach_absolute_time();
 [obj release];
 NSLog(@"[[MyClass foo: %g", timeScaleSeconds*(stopTime - startTime));


    
 mach_timebase_info_data_t timebaseInfo;
 double timeScaleMicroSeconds, timeScaleSeconds;
 if (mach_timebase_info(&timebaseInfo) == KERN_SUCCESS) {    // returns scale factor for ns
     timeScaleMicroSeconds = ((double) timebaseInfo.numer / (double) timebaseInfo.denom) / 1000;
     timeScaleSeconds = timeScaleMicroSeconds / 1000000;
 }


Also, I found that whichever class I timed first ran longer than the others, so I adjusted by running a dummy loop first and not reporting its time.  The above data incorporates this adjustment already.  Compiler was optimizing to -Os.

*Ah, good that you've done this, I was thinking of doing it myself just to finally disspell that old myth that code runs faster if your variable names are shorter (which this is a variant of). I figured that you would get different result depending on which class you tested first, and it's good that you have accounted for that. Dunno why it would, but somehow I had a feeling that the runtime would behave that way =). --Theo*

----
Everyone wants Apple to "fix" this. Is Objective-C Apple's own baby? I'm not sure they "own" it. There are the GCC and GNUstep projects which would allow the Open Source community make such changes. Consider that Apple uses GCC, and that might lead this conversation elsewhere.

Anyway, my opinion is that @compatibility_alias is the way to go until the community agrees on a method of "fixing" Objective-C. --SigmaD

