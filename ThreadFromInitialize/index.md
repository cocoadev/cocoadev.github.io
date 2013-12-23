---
layout: page
title: ThreadFromInitialize
---

How do I start thread off the class initialize method, with General/NSThread?

It is clear, that I can't just use
General/[NSThread detachNewThreadSelector:@selector(thread) toTarget:self withObject:nil];

because there is no any self here.

I can use pthread_create, but it doesn't looks like cocoa-path, yeah?

Is there any solution, except pthread_create?

// General/SuperCat

----

There *is* self in a class method, self is the class. It is perfectly fine to use General/NSThread in that way from a class method, if the selector you give it corresponds to a class method.

----

Hmm, I don't understand either the question or the answer here, despite being pretty comfortable in cocoa.  Can you explain more clearly what you're trying to do?  What message would you send if you weren't trying to do it in a new thread?

Ah, I think I see.  I'm guessing that you are trying to call a method +General/[MyClass thread].  A class is an object like any other, and in a class method,     self refers to the class object.  You can get ahold of the class object for     General/MyClass with the message     General/[MyClass class].  So you may be okay to use the form that you wrote above, or you may need to say

    General/[NSThread detachNewThreadSelector:@selector(thread) toTarget:General/[MyClass class] withObject:nil];

depending on whether you are trying to call this     thread method from a class method or an instance method.

----

All is working good, there was mistyping in declaration. Sorry.
