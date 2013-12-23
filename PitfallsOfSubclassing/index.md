---
layout: page
title: PitfallsOfSubclassing
---

Subclassing apparently has pitfalls. Anybody care to let us in on 'em?


*Existing code has to be modified to take advantage of your SubClass. If that's not what you want, try ClassPosing or ClassCategories.
*With some classes (notably Apple's classes), you have to be careful to implement a few methods in your SubClass. Sometimes it's not so clear which methods these are.
*As above, ClassClusters require care.


Subclassing can be tricky because your subclass can (potentially) be used in the same circumstances as the superclass, therefore it should have the behaviors (that is, keeping the same contracts.  Also known as the Liskov substitution principle) as the superclass.  It also tightly binds your new code with the superclass, meaning a change there (like in the cocoa libraries) can change the semantics of your subclass code, breaking things.

Also, because languages like objective-C and Java lack multiple inheritance, if you subclas to do something, then discover this class needs to be a subclass of something else, you're screwed.  If you avoid subclassing by protocols or categories, you retain flexibility to change the object's superclass in the future.

