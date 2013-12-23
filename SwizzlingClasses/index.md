---
layout: page
title: SwizzlingClasses
---



Simply put, General/SwizzlingClasses is changing the class of an object after it has been allocated and initialized.

This has been used, for instance, in the General/EnterpriseObjectsFramework.

It is recommended that you avoid this procedure, because it's extremely dangerous unless you have *very* good control of the circumstances.

----

Ok, it's dangerous, but how do I do it?

**I think I might have made this page originally. I didn't say, cos I haven't the foggiest. I suppose you might set your object's isa variable...**

----

Something like this:

    
#include <objc/objc-runtime.h>

// And then in your code, where anObject is, well, an object, and newClass is the new class (a variable of type Class)
anObject->isa=newClass;


To get the new class use something like + (Class)class or General/NSClassFromString(General/NSString *aClassName)

You could do anObject->isa=anObject->isa->super_class to change an object to the superclass of its current class.

General/SamTaylor

----

A small note in the Cocoa Bindings docs says that bindings works by swizzling the class of an observed object, although I am too lazy to search for the link right now -- l0ne aka General/EmanueleVulcano
