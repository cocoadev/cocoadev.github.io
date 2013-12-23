---
layout: page
title: AspectCocoaDocumentation
---

AspectCocoaDocumentation - Documentation for AspectCocoa

Creating an Apsect consists of three pieces: an AdviceObject, a PointCut, and the Aspect(ACAspect) itself

From the viewpoint of an Aspect, a running program is simply a sequence of method calls on classes.  An Aspect combines a PointCut (which defines some subset of all method calls on all classes) with an AdviceObject (which defines some functionality to be performed before, after, or in place of an intercepted method call).

AspectCocoa classes: [Topic]

That's it!  Everything you need to know about AspectCocoa... now go make something cool

----

for an example of hands on code for creating AspectAdvice, PointCuts, and loading an ACAspect see InvarianceCheckingWithAspectCocoa

