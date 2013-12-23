---
layout: page
title: NibOutletMethods
---

In the relatively short while I've been coding in Cocoa, I've always provided accessor and mutator methods for my IBOutlet<nowiki/>s.  This is as per the documentation, which says that NSNib looks for the mutator first, falling back on directly setting the InstanceVariable if a mutator is not found.   Since the default scope of ivars is protected, I assumed that I was required (and it would be safest) to provide accessors and mutators for all of my outlets.

Any time I've seen code involving IBOutlet<nowiki/>s, there are never any accessors or mutators, nor @public directives.  I would love to dispense with the annoying practice of implementing stub accessors and mutators, but how will NSNib set my ivars?  Does NSObject provide some default implementation for KVC-style accessors and mutators that will spare me?

----
Yes, it does. No doubt the fine manual for valueForKey: and setValue:forKey: will elaborate.
----
A) Historically, nib loading used the Objective-C runtime C API to directly set outlets if no accessor/mutator was availabe.

B) @private and @protected and @public are just compile time hints in Objective-C.  They have no impact whatsoever at runtime.

C) Use Accessorizer to automatically generate accessors/mutators.  It is a good practice to have them and use them even if you don't need them.  Don't even access your own instance variables directly in your own non-accessor methods.  Always go through the accessors.  It will save you headaches later when you want to change something, and it costs you nothing now thanks to accessorizer.  http://www.kevincallahan.org/software/accessorizer.html
----
Thinking about it, it figures that NSNib would do anything in its power to set outlets.  Thanks a bunch.

----
"It is a good practice to have them and use them even if you don't need them."

I would generally disagree with this. I believe that strict accessors violate encapsulation by effectively making the ivar public. By not writing accessors until you need them, you encourage thoughts toward possibly more OO and encapsulated ways of accomplishing what you need, rather than just writing code that twiddles a bunch of accessors.

Of course the nib loading mechanism violates encapsulation horribly, but in this case I think the ends justify the means.
----
Accessors don't violate encapsulation.  There is no rule that says accessors need to be documented or exposed outside the class implementation.  Accessors in fact greatly enhance encapsulation of instance variables by decoupling the access and the underlying storage.  If you have accessors, you can safely change an implementation to use a dictionary instead of real instance variables or switch to using Core Data under the surface or calculate values on the fly instead of storing them.  If you directly access your instance variables throughout the implementation of your class, you just make it harder to re-factor and/or change the underlying storage when you want to.

Furthermore, accessors play a critical role in Cocoa's memory management conventions: http://www.stepwise.com/Articles/Technical/2002-06-11.01.html/

If your classes are just big bags of publicly accessible data, then I agree that encapsulation has been violated.  If you provide accessors that are only used within the implementation of your class, there has been no violation of encapsulation.

