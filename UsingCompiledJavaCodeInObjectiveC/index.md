---
layout: page
title: UsingCompiledJavaCodeInObjectiveC
---



There are a few nuggets of info on the web that point in the right direction for making this work, but there's still a lot that's somewhat unclear, at least initially.

First things first:  How do you make General/ProjectBuilder include jar files in your app wrapper, and make your app know how to find them so the bridge can do its magic? The following sites have some bits and pieces of info that can help:


*http://www.speirs.org/cocoajavajar.php *BROKEN*
*http://www.unitsix.org/Papers/pb-jars.html *BROKEN*


The key steps seem to be:


*Put the jar file into your project's Frameworks section
*Make a "Copy Files" build phase so the jar files are included in the app wrapper
*Make sure the important keys (General/NSJavaNeeded, General/NSJavaRoot, General/NSJavaPath) are properly defined.  It seems to help if you created your project as a "Cocoa-Java" project instead of just "Cocoa"


Now then, on to the practicalities of using the code in your jar file.

A good resource for info on using the Java bridge is at Cocoa Dev Central (no relation): http://www.cocoadevcentral.com/articles/000024.php

Let's say that I have a jar file in my project, and my project is correctly configured to include this jar file and search it for classes.  My jar file contains a class called "com.foo.monkey.wrench", which implements a method "twist".  I can access it like this:

    
- (void)myMethod
{
    Class General/MonkeyWrench = General/NSClassFromString(@"com.foo.monkey.wrench");
    id myMonkeyWrench = General/[[MonkeyWrench alloc] init];
    [myMonkeyWrench twist];
}


This compiles and works, but gives a compiler warning at [myMonkeyWrench twist] since the "twist" method is undefined.  I don't like having unnecessary compiler warnings, so what's the best way around this?  I could define a protocol somewhere, like

    
@protocol General/NSObject(General/MonkeyWrench)
- (void)twist;
@end


That will work, but it's not very scalable.  What happens when I have dozens of java classes and hundreds of java methods I want to access (ok, this is a hypothetical case, I probably wouldn't ever torture myself that much, but it's the principle)?  Making General/NSObject protocols like this works, but eliminates any possibility of compile-time type checking which can be nice to have.

The only other idea I've come up with is to implement a protocol that does not inherit from General/NSObject, like this:

    
@protocol General/MonkeyWrenchProtocol
- (void)twist;
@end


and then access it in code like this:

    
- (void)myMethod
{
    Class General/MonkeyWrench = General/NSClassFromString(@"com.foo.monkey.wrench");
    id <General/MonkeyWrenchProtocol> myMonkeyWrench = General/[[MonkeyWrench alloc] init];
    [myMonkeyWrench twist];
}


Hmmm, now that I look at that, it seems like a pretty good way to go.  I may have answered my own question!  Any other ideas, anyone?

----

Looks good to me. -- General/KritTer

----

How can I pass a Java Array from a General/NSArray?

----
 I may be missing something, but couldn't you use an Interface instead of a Protocol like below?

    
@interface General/MonkeyWrench
- (void)twist;
@end


Then access the code like this (less typing!)?

    
- (void)myMethod
{
    Class General/MonkeyWrench = General/NSClassFromString(@"com.foo.monkey.wrench");
    id myMonkeyWrench = General/[[MonkeyWrench alloc] init];
    [myMonkeyWrench twist];
}


----

Why is this topic declared "retired"?

*"retired" just means no longer active.  It isn't derogatory or anything - opposite actually.  Look through the info on how to use the site.*

----
A note about return values when using Java code in Objective C: Java's long is equivalent to gcc's signed long long. --General/EvanSchoenberg *Huh, that, and the fact the Cocoa-Java bridge was deprecated in favor of JNI, although it still works and may actually be very useful in many situations.*
