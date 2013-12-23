---
layout: page
title: TemplateMethod
---



General/TemplateMethod is a General/DesignPattern which defines the steps of an algorithm and allows another class to provide the implementation for one or more steps.  This is typically implemented by using an General/AbstractSuperclass which calls abstract methods from its template methods, effectively deferring steps in an algorithm to its subclasses.

General/TemplateMethod is useful for:


* Adding hooks into an algorithm for notification purposes, similar to General/DelegateObject<nowiki/>s.
* Keeping the steps of an algorithm intact while hiding the implementation details of the rest of the algorithm from the object that implements some of those steps.


----
**Using abstract classes for General/TemplateMethod**

Cocoa does not have an explicit way to declare an General/AbstractSuperclass or abstract methods, but these can be simulated by throwing exceptions in the "abstract" methods and throwing an exception in the default initializer if the class being initialized is the "abstract" class (instead of a subclass).  

See General/HowToDeclareAbstractClasses and General/WhyDeclareClassAsAbstract.

----
**Using a General/DelegateObject to mimic General/TemplateMethod**

Delegation in Cocoa is similar to General/TemplateMethod, but in most cases delegates are optional, and the result of the delegate call is not crucial to the proper functioning of the algorithm. However, use of the General/TemplateMethod often involves cases where the algorithm cannot function properly unless the abstract method is implemented (or the delegate responds to the method).

The best way to mimic General/TemplateMethod with delegates is to force delegates to implement a General/FormalProtocol for non-hook delegate methods and require the delegate as an initializer parameter. 

This approach is very similar to the Strategy General/DesignPattern.

----
**Discussion:**
