---
layout: page
title: WhatIsIMP
---

What's IMP?  And can I use it from a method to obtain the selector of the method within which the calling code resides?

----

An IMP is an implementation. That is, it's a pointer to the actual C function implementing the method, bypassing the regular     [object message] syntax and the whole dynamic binding thing.

If you want the selector, just use     @selector(message).

-- General/RobRix

----

If you want the selector of the method within which the calling code resides, better yet, use     _cmd. The IMP takes at least two arguments, the first sets     self (quite useful!!) and the second is     _cmd, so you get them both in your method.
General/CharlesParnot

----

Sometimes you'll see people cache General/IMPs because it speeds things up. I'd be wary of this. It makes a difference when you are calling a method more than once, say calling it for all objects in an array. General/ObjectiveCee primarily uses selectors to select methods, so what if one element of the array uses a different IMP for the original selector? You'll use the wrong IMP. - General/MikeAmy
