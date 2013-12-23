---
layout: page
title: AspectCocoa
---

**Now Open Source** - http://www.ccs.neu.edu/home/igotimac/AspectCocoa-src.zip

http://www.ccs.neu.edu/home/igotimac/aclogo.gif

**AspectCocoa, an Aspect Oriented Programming Framework for Cocoa and Objective-C.**

An on-going research project at Northeastern University.
Lead personel:  JacobBurkhart and David H. Lorenz.

WARNING!! AspectCocoa leaks memory, leads to oversized binaries, and generally is not a reliable production quality piece of software (it's best used for debugging and inspection, and then removed before compiling the final version of your software).

Aspect Cocoa is available for download at:
http://www.ccs.neu.edu/home/igotimac/AspectCocoa.zip
After downloading the framework, read the AspectCocoaTutorial to get started.

AspectCocoaDocumentation is a work in progress, contribute ideas for AspectCocoaProjects, and tell us about your problems in AspectCocoaBugs.

Please feel free to post your own ideas about what we're doing!

----

The idea of Aspect Oriented Programming (AOP) is to be able implement some similar functionality pertaining to a large number of classes/methods without having to go into every relevant method declaration and add code.  Instead, we write this kind of code in a separate file, and then load it into the appropriate methods at compile-time or at runtime.  We call the process of writing this added code and defining where it should be: writing an "Aspect".  We call the places where this code should be added "Point Cuts".  And we call the functionality of this added code "Advice".

For those that want an overview with a few more details, see Mitch Wand's slides (PPT) from his ICFP '03 Invited Talk:
[http://www.ccs.neu.edu/home/wand/papers/icfp-03-slides.ppt]

----
**For Example:**  (also see: AspectCocoaExamples)

Say we have a Cocoa program like Fire which we just downloaded from SourceForge.  Now, say we want to start making use of the Fire source code, and learn how the whole thing works.  We could read through the documentation or something like that, but we're programmers.  So, instead we just write an Aspect to help us.  We will NSLog every method call, but we're going to restrict it to methods defined by the Fire source code, and ignore Appkit and Foundation calls.  So, now we can run Fire and watch in ProjectBuilder or XCode as every method call gets logged out.

You'll notice in the above example that we don't really say HOW we write this Aspect, we just say that we write it.  So, part of what we are trying to do is come up with concrete ways in which an Aspect might be written.  If you wanted to implement what I just described in a very dumb manner you could go through all of the Fire source code and insert NSLog statements at the beginning of every method.  Clearly, this is not a very efficient thing to do.  If you wanted to be a little smarter, you could create a Class, let's call it MyFirstAspect.  In MyFirstAspect you could define a method something like this:
    -(void)beforeMethod:(SEL)selector; 
which simply logs out the selector.  And then instead of inserting NSLog statements into every method we could insert something like this:  
    MyFirstAspect sharedInstance] beforeMethod: _cmd];
Now, we have the same functionality as before, but we can now change what happens before every method call without having to go and change every method, we just have to change **beforeMethod:**.  

Now, Imagine a framework that automatically adds and removes those **beforeMethod:**' calls from each method you're interested in and does it dynamically at runtime.  That's [[AspectCocoa.
----

Much of the content already posted to this wiki has been for the discussion of the problems we've encountered while creating AspectCocoa.  Now that we have a working version of it available, we will start adding new content to document how it works.  All old and outdated content has been moved to AspectCocoaOld.

----

I have a project wherein the capabilities of aspects would come in useful. However... when is the source code going to be released? I've no interest in embedding a framework with code of this nature (introspective/reflective) that I can not see into my product. Tools such as this should be open for anyone to utilize it. Opening the source would likely get more people using and disucssing your excellent work. I currently see very little in the way of comments and complexities of working with multiple representations of objects in persistence (ie., Obj-C = NSObject -- Class and CoreData = NSManagedObject -- NSEntityDescription) are likely to increase. Making more tools and systems available will also help Windows developers when they finally give up and switch. -- da5id

----

Any response for the above question?  Especially as the authors state that it is hazardous to use, I'd think they'd want to make the source available.  I'd love to contribute, but I can't unless it's a public project. -- silverfields1

----
http://www.ccs.neu.edu/home/igotimac/AspectCocoa-src.zip
--or--
http://rapidshare.de/files/28773033/AspectCocoa-src.zip.html

I have just released the source, somebody please take charge of this project and post it somewhere more permanent. - JacobBurkhart

