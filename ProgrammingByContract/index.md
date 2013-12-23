---
layout: page
title: ProgrammingByContract
---

ProgrammingByContract

a main feature of the EiffelProgrammingLanguage

This goes beyond type checking and method signatures in many ways to make sure that your code does what it says it will, without extra side effects (at least in Eiffel).  The idea is that you write, in addition to the method signature, a set of conditions about the arguments that must be true for the method to function properly, as well as a set of conditions that will be true when the method completes (including what effects the method performs, and the state of the return value).  This set of statements is called the contract.  Like a real life contract, it sets out the things that must be true for the method, both the benefits the caller recieves (the actions performed, or the return value recieved) as well as what the caller must provide for the task to be completed.  The EiffelProgrammingLanguage has very nice syntax for making this part of your code, in other languages though you can still apply the concept (it just requires a little more work).

The first step to applying ProgrammingByContract to ObjC is writing the contract in the interface, maybe with HeaderDoc comments for each method.  The second step is adding the checks for the contract to the begginning and end of the method implementation.

Another name for contracts are pre/post conditions.

A related concept is class invariance.  This is a set of statements that your class or an instance of your class requires to be true to function properly.  A common way to implement this in ObjC is to add an invariant method that tests the conditions it requires as well as the conditions laid out in the super class, and then to call this method at the start and end of every applicable method.

----

This remind me of UnitTests and TestDrivenDevelopment.  The difference being that unit tests test from the outside only for situations you tell it to check, whereas contracts are inside the method and test every call of the method.  The goal of ensuring correct functioning of every part is similar, and the costs of either technique should be weighed.  Contracts are runtime checks that would add the call everytime... where as UnitTests can be run seperately from your final product, and thus incur no runtime cost at all.

*Indeed. Which begs the question, could you use something like AspectCocoa to build a unit testing framework that would be used at runtime to program by contract, but which could be turned off for deployment releases (if you so chose) to improve execution times?*

We're trying: InvarianceCheckingWithAspectCocoa (to start... and then later pre/post-condition checking?)

----
for pre-post conditions that you want to turn off for deployment, use NSAssert(condition,message) and in your deployment build style make sure it #define-s NS_BLOCK_ASSERTIONS

