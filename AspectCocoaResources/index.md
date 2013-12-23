---
layout: page
title: AspectCocoaResources
---



*http://www.ccs.neu.edu/home/lorenz/center/aop.html
*http://www.iturls.com/English/TechHotspot/TH_e.asp

MethodSwizzling provides some useful information pertaining to my implementation...

A user of an aspect will define certain methods/classes or categories of methods or classes, which are to be overriden by a special method that optionally does something, then calls the original method, then optionally does something else.

The definition of what and when and where to override is called a PointCut.
and the optionally does something is called Advice. (defined by an AdviceObject)

A simple example of what an Aspect could do:
Whenever any method on NSObject is called (not it's subclasses), print out the name of that method...
I'm also going to need this:
http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/4objc_runtime_overview/chapter_4_section_6.html
and the: _cmd pointer and the stuff in: /usr/include/objc

also, I'd like to find a replacement for:
    
    NSMethodSignature * signature = [theClass instanceMethodSignatureForSelector: selector];
    int num = [signature numberOfArguments];


----
**Replacement**: How about      method_getNumberOfArguments .

You may want to review the chapter on *Objective-C Runtime Functions and Data Structures* at http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/
----

so that I don't have to rely on everything being a subclass of NSObject .. although I could parse the selector to look for the number of :'s if it were a string...
how do I turn a SEL into an NSString

**NSStringFromSelector(). It's documented in the Foundation Functions page, but is fairly obvious.**

