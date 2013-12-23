---
layout: page
title: AspectCocoaProjects
---

AspectCocoa is a way to intercept method calls and apply advice, this section is for all of you out there that think AspectCocoa might be usefull for something but don't know how to do it yourself to provide suggestions to help us whip up some good examples.
----

InvarianceCheckingWithAspectCocoa

----
**A better Undo Manager.**
The current undo manager requires that every method put itself on the undo stack.  Suppose we could instead simply define a set of methods with a corresponding set of undo methods, and use AspectCocoa to intercept these relevant method calls to put them on the stack when invoked.

Does this sound useful or interesting?

*Sounds interesting. If I am understanding you correctly, the advantage is not having less code, but instead having all of the undo code in one area instead of scattered throughout several different classes. Now that I think about it, won't this require more code because AspectCocoa needs to know both the "trigger method" and the "undo method"? The only other disadvantage that I can think of is it may add some complications while debugging and optimizing because code is being called which is not actually contained in the called method. Nothing comes without a price I suppose. -- RyanBates*

----

Let me add to the above, before the first mutating method is sent to the model then we may wish to take a snapshot of some meta-info (like what was selected, so as to restore that on undo), likewise, after the last mutating method is executed we may wish to "merge" all the undo actions recorded.

in AspectJ that could be done with an aspect "around" (or after) the first method sent to an instance of our controller (since that message is sent by the event loop) -- I do not know if AspectCocoa allows to define such PointCuts? i.e. based on caller and callee.

Additionally we may also decide when to     setNeedsDisplay: based on an aspect (i.e. when the model receives a mutating method).

----

I was interested in the replacement method to add aspects. Do somebody attempted to implement this by variable-argument replacement methods, something like:
    
id genericReplacementMethod(id obj,SEL sel,...)
{
                   va_list ap;
                   int d;
                   char c, *p, *s;
                   char *fmt = GetArgTypesForObjectAndSelector(obj,sel);
                   va_start(ap, sel);
                   while (*fmt)
                           switch(*fmt++) {
                           case 's':                       /* string */
                                   s = va_arg(ap, char *);
                                   printf("string %s\n", s);
                                   break;
                           case 'd':                       /* int */
                                   d = va_arg(ap, int);
                                   printf("int %d\n", d);
                                   break;
                           case 'c':                       /* char */
                                   c = va_arg(ap, char);
                                   printf("char %c\n", c);
                                   break;
                           }
                   va_end(ap);
   return nil;   
}

noting that IMP is defined as
    
typedef id                      (*IMP)(id, SEL, ...); 

in     /usr/include/objc/objc.h. This would (I think) solve the problem of generating code for all the message signatures. It will work? 
MassimilianoGubinelli

