---
layout: page
title: AspectCocoaOld
---

The Following: LookupAspects and SingleInstanceAspects, are the AOP for Cocoa possibilities currently being pursued. So far, the other approaches to solving the problem of enabling aspects for Objective-C are too restrictive.

**LookupAspects**
*Loaded at runtime.
*Can override any number of methods on any class with any number of AdviceObjects.  
*Calling the original method is done with a lookup via the ACAspectManager.
*Has concept of loading and unloading Aspects.
*Allows loading multiple aspects on a single class/method.
*Lookup for finding original IMP of called SEL results in additional overhead when loading/unloading aspects or looking up advice.
*Requires no knowledge of the class being modified.  
*Implemented by scanning through a class and replacing methods with appropriate "replacement" methods (declared elsewhere in AC classes) which use a lookup to call the original method, and on before: and after: methods on all relevant AdviceObjects.
*A work-in-progress example: http://www.ccs.neu.edu/home/igotimac/LookupAspect.zip

**SingleInstanceAspects**
*Loaded at runtime.
*Can override any number of methods on a single instance of a class (created, or yet-to-be-created).
*Implemented by dynamically creating a subclass of the instance-to-Aspect.
*Calling the original methods is done with a lookup of that method on [self superclass].
*Calling Advice is done with a call to the ACAspectManager for the relevant AdviceObjects.
*Aspects may be loaded and unloaded.
*Overhead approximately equal to that of LookupAspects.
*Requires no knowledge of the class being modified.

----

Other approaches considered:

**PoseAsAspects**
*Loaded at runtime.
*Can override all methods of a class without requiring specific declaration of each class.
*Originally thought we could call the original method with a call to super, but we'll have to instead lookup the IMP via [self superclass]. yuck
*Aspects must be loaded, but has no concept of unloading Aspects.
*Allows loading of only one aspects per class.
*Low overhead, but only after aspects have been loaded.
*Requires no knowledge of the class being modified.
*implemented by declaring a new class in the runtime as a subclass of an existing one, declaring methods in this new class to override the super classes, plus additional before and after methods. And then uses the PoseAsClass functionality to replace the original class in the runtime.
*A working example: http://www.ccs.neu.edu/home/igotimac/PoseAspect.zip

**SimpleAspects**
*Loaded at runtime.
*Require that every method of every class be declared (with the same SEL as the method it's overriding) and behavior described.  
*Calling the original method is done here with a call to self.  
*Has concept of loading and unloading Aspects.
*Allows loading of only one aspects per class/method.  Lowest overhead, but only after aspects have been loaded.
*Requires some knowledge of the class being modified.  
*Implemented with categories (to add methods to a class), and swapping method IMP pointers.  Also see, MethodSwizzling.

**AddMethodAspects**
*Loaded at runtime.
*Since PoseAs won't work quite like we wanted it to. we need something that can be loaded and unloaded at any time and apply to everything.
*A working example: (New!) http://www.ccs.neu.edu/home/igotimac/ReplaceAspect.zip

**AddClassAspects**
*Loaded at runtime.
*Another idea.
* A working example: http://www.ccs.neu.edu/home/igotimac/AddClassAspect.zip

**ForwardInvocationAspect**

**CompiledAspects**
*Loaded at compile-time.
*Beyond the scope of this project at the momment.

----

Along the way I've encountered alot of strange behaviors that have led me to ask some AspectRelatedQuestions
also see: AspectCocoaResources.

Right now we are working on a set of AspectCocoaRequirements to put the whole thing together into something useful.  After that we'll need to come up with an AspectCocoaSyntax.

Checkout AspectOnSketch (the example program that comes with dev tools) and it's AspectOnSketchOutput as an example of what an aspect can do.

**Jacob:**  This piece on overriding may be of interest to you:  http://rentzsch.com/papers/overridingMacOSX  (Sorry, but I didn't have time to look up an email adress...)

This looks pretty cool.  What is the driving motivation?  What are the problems you expect folks to solve?  (like a concrete one-liner would help me understand what this is all about)

*This might not be something that would even be thought of by most folks, but the ability to specify cut points like this is a boon for people (like me) who are working on adaptive networks (as described in Steve Grand's book Creation: Life and How to Make It). Adaptive networks are networks of neuron-like objects, and being able to (easily) specify cut points like, for instance, the firing of neurons lets you apply logic across the network on all neurons, or specific classes of neurons, as you wish. This can be useful for e.g. weighting the firing of neurons. Now, since I'm writing the adaptive nets myself, I don't strictly need aspects to do this, but they're a possible solution.*

11/25/2003
Could this be used to benchmark your code and put a timer for every method call you are interested in? The possibility of doing it at runtime and to load/unload at wish would make it even more powerful.
CharlesParnot

Charles - Yes, this certainly would be a good use for Aspects... And pretty simple to do with the LookupAspects library, look for: AspectsForBenchmarking coming soon.. (the timers wouldn't be as accurate as if you were to add them into your code(method calls) yourself because of the time involved in looking up the original method. But that time would be relatively constant, so therefore possible to factor out.

