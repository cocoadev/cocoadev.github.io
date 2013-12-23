---
layout: page
title: InformalProtocols
---



InformalProtocols are constructs which allow you to share a common programming interface between two classes without them having to inherit from any specific object (except for NSObject).

They are generally made with a category on NSObject that has no implementation associated with it, but if you had reason to, you could restrict it to another class (but would there really be a point?).

See FormalProtocols for a brief discussion on the differences between FormalProtocols and InformalProtocols.

See ProtocolsTooCasualSmell for a discussion on when InformalProtocols are bad; see also CategoriesAreBad for general category discussion.

----

An example:

To 'define' an informal protocol, MyCustomViewDelegate you would usually say

    
@interface NSObject (MyCustomViewDelegate)

- (id)myCustomView:(MyCustomView *)aCustomView parameterWithObject:(id)anObject;

@end


and that's it.  I'd usually put this declaration on     MyCustomView.h.

What does it do?  

* It tells the compiler about the new methods and their signatures.  That'll make it stop warning you when you say 
    [someObject myCustomView:view parameterWithObject:anObject].
* It tells clients what methods they ought to implement.
* Not much else.  


Note that we don't use     @protocol at all.  That's for a FormalProtocol.  The name of the protocol is not something
you can use at runtime either.  Basically, this just shuts the compiler up and trusts you to make sure everything
works.

Okay, so say you want to use MyController as a MyCustomViewDelegate. What do you do?

You implement     - (id)myCustomView:(MyCustomView *)aCustomView parameterWithObject:(id)anObject on     MyController.m.  
It doesn't matter whether you include a declaration of the method on     MyController.h since it's already been declared as
a method of NSObject. Declare it again if you think it illuminates the use of the object.  Again, nowhere does something like     @implementation MyController <MyCustomViewDelegate> appear, that's used with FormalProtocols.  This is really just a category that we happen to use like a protocol.

(Question moving to InformalProtocolQuestion - it was distracting here)

----

*You don't use brackets when declaring methods in ObjC, and there has to be an @end. Code snippet has been corrected (again).* **Hum.  The IP address logs say I killed your change, but I didn't mean to.  I guess I was editing something else?  Thanks for the catch.** *Oh, sorry about the slightly-harsh words then. When are we going to get automatic conflict detection here? Sigh. I guess you can delete this whole part after reading.*

