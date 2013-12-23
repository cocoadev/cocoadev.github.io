---
layout: page
title: AddMethodAspects
---

AddMethodAspects - part of AspectCocoa

The idea here is that you could call     [ACReplaceAspect wrapAllMethodsOfClass: [MyClass class]], and get a nice little stack trace of all the methods being called in your class.  You can get an example of something that should work here: http://www.ccs.neu.edu/home/igotimac/ReplaceAspect.zip  But for some reason the extra replace, before and after methods aren't getting added to MyClass.  Presumably this would be a much more interesting example if     class_addMethods() actually worked right, and we could put an aspect on something like NSObject or NSDocument.

**Implementation summary:**  when applying an aspect to a class, for every method we will define a new method replace_(insert old method name here) . the IMP for the replace_ methods will be the original IMPs, and the IMPs for the orginal methods will be defined "replacement" methods that call before, after, and call something like [self replace_(original)] to get the original method.  This should also allow adding additional aspects, resulting in methods with additional replace_ prepended to the method name. (we have not yet accounted for adding multiple before/after methods)

