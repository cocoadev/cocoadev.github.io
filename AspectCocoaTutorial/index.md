---
layout: page
title: AspectCocoaTutorial
---

A Tutorial for using AspectCocoa.

The framework as availble from http://www.ccs.neu.edu/home/igotimac/AspectCocoa.zip is compiled as an embedable framework.  This tutorial assumes you are using XCode on Tiger, but there's no particular reason why it won't work on older setups. (If you're trying to get it working on an older setup, you can ask me for help JacobBurkhart). 

So to get started, download the AspectCocoa framework, and open a version of Apple's example program Sketch.

Part one, compiling Sketch with the AspectCocoa framework:
1. Add the framework to your project:
find the "External Frameworks and Libraries" folder. right-click.. Add... Existing Frameworks... select AspectCocoa.framework
2. Add a Build Phase to copy the framework into your executable:
goto Targets.. Sketch.. right-click... Add... New Build Phase... New Copy Files Build Phase.
Select Destination: Frameworks
(close inspector)
3. drag AspectCocoa.framework into the Copy Files Build Phase.

At this point, you should be able to compile and run Sketch.

Part two, Generating AspectCocoa method stubs:
Edit Sketch_main.m, and add the following Code:
To start with, it should look something like this:
    
int main(int argc, const char *argv[]) {
    return NSApplicationMain(argc, argv);
}

This main function is the most convenient place to start messing around with Aspects, because it allows us to configure and load then before anything else happens.
Unfortunately, since we'll be putting code in before NSApplicationMain is called, we'll need to setup our out autorelease pools. (ohh and we'll also need to import Aspect Cocoa)
so...
    
#import <AspectCocoa/AspectCocoa.h>
int main(int argc, const char *argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    //this is the best place for doing all your AOP
    [pool release];
    return NSApplicationMain(argc, argv);
}

Next, let's make a point cut.
    
    ACPointCut * pointCut = [[ACPointCut alloc] initDefault];

This "Default point cut picks out all methods on all classes that are not park of AppKit or Foundation.  So, in this case, it picks out all methods on all classes defined in Sketch.app.
Alternately, we could pick out everything with:
    
    ACPointCut * pointCut = [[ACPointCut alloc] init];


Now, we neeed to generate those method stubs we were talking about. our main should now look something like this.  (you should change @"/Users/igotimac/Sketch/generatedAdds/SketchAOPAdditions" to point to some convenient folder insider you Sketch project)
    
#import <AspectCocoa/AspectCocoa.h>
int main(int argc, const char *argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    ACPointCut * pointCut = [[ACPointCut alloc] initDefault];
    [ACCodeGenerator generateCodeForPointCut: pointCut 
                    writeTo: @"/Users/igotimac/Sketch/generatedAdds/SketchAOPAdditions"];

    [pool release];
    return NSApplicationMain(argc, argv);
}

We will run Sketch, once. and we should see something like this logged out:
    
2005-07-22 10:37:55.848 Sketch[1010] writting in pieces
2005-07-22 10:37:55.849 Sketch[1010] count 90
2005-07-22 10:37:55.849 Sketch[1010] 0 from 0 length 90
2005-07-22 10:37:55.849 Sketch[1010] writting
2005-07-22 10:37:55.849 Sketch[1010] category is SketchAOPAdditions0

Which means that 2 new files have been created, and should now be included i your project:
SketchAOPAdditions0.h and SketchAOPAdditions0.m
import then as you see fit.

If you're concerned about some error messages like: 
    
2005-07-22 10:37:55.847 Sketch[1010] couldn't find code/string for type CGRect

Don't be.  This means that AspectCocoa could not create a method stub for some method that takes a CGRect struct as an argument (or return type).  While we currently support some typical foundation structs like NSRect. We do not support user defined structs at the momment.  All this means, is that you won't be able to advise a method that takes a struct as an argument unless that struct is one of the common foundation structs.

So anyway... we should remove that generate code statement since we won't be needing it now that we have the code we want.
And, now we'll actually create and load an aspect.
so out final main should look like:
    
#import <AspectCocoa/AspectCocoa.h>

int main(int argc, const char *argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    ACPointCut * pointCut = [[ACPointCut alloc] initDefault];
   //[ACCodeGenerator generateCodeForPointCut: pointCut 
   //writeTo: @"/Users/igotimac/Sketch/generatedAdds/SketchAOPAdditions"];
   
   ACAspect * myAspect = [[ACAspect alloc] initWithPointCut: pointCut 
                               andAdviceObject: [ACLoggingAdvice new]];
   [myAspect load];

    [pool release];
    return NSApplicationMain(argc, argv);
}


Now when we compile and run Sketch, we should see debug logs galore, like this:
    
2005-07-22 10:45:52.356 Sketch[1232] -before graphicWithPropertyListRepresentation: on SKTGraphic
2005-07-22 10:45:52.356 Sketch[1232] --before init on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ---before undoManager on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ----before document on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ----after document on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ---after undoManager on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ---before undoManager on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ----before document on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ----after document on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ---after undoManager on SKTRectangle
2005-07-22 10:45:52.357 Sketch[1232] ---before undoManager on SKTRectangle
2005-07-22 10:45:52.358 Sketch[1232] ----before document on SKTRectangle
2005-07-22 10:45:52.358 Sketch[1232] ----after document on SKTRectangle
2005-07-22 10:45:52.358 Sketch[1232] ---after undoManager on SKTRectangle
2005-07-22 10:45:52.358 Sketch[1232] ---before undoManager on SKTRectangle



Not that that's particularly usefull to you... and you're probably annoyed by the repeated:
    
2005-07-22 10:44:57.885 Sketch[1232] couldn't find code/string for type CGSize

messages


So the tutorial shall continue another day. And maybe do something usefull. In the meantime, you can look at the rest of AspectCocoaDocumentation and see if you can make it do something you find interesting

