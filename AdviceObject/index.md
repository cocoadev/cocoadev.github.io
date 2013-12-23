---
layout: page
title: AdviceObject
---

An AdviceObject is the standard way of applying advice to some point cut in AspectCocoa.

Any object can be an AdviceObject.  The most basic one provided by AspectCocoa is ACLoggingAdvice.

In order to apply advice, an AdviceObject is expected to respond to either     before:,     after: or     around:

here's an example implementation of     around: which wraps the invocation with an exception handler
    
-(void)around:(ACInvocation  *)invocation{
    NSLog(@"invoking");
    NS_DURING
        [invocation invoke];
    NS_HANDLER
        NSLog(@"failed to invoke %s on %@",  [invocation selector], [invocation target]);
    NS_ENDHANDLER
}


The types for     before: are     after: are the same as     around:.  they take one argument which is an ACInvocation object.
If it's around advice it must explicitly call invoke on the invocation, or it will not be invoked... if it's before or after advice it should simply use information provided by that invocation (such as it's arguments or return value) to perform some functionality, but it can call invoke if it wants to (calling invoke more than once on the same invocation has no effect)

    -(void)around:(ACInvocation  *)invocation is very very similar to     -(void)forwardInvocation:(NSInvocation  *)invocation except that instead of forwarding some method call, we are intercepting it (and possibly redirecting it).

----

**Comments**

So this is also a way to have a fastForwardInvocation!! This is great!

no, it's not the same as forwardInvocation... calls to forwardInvocation are triggered by a method lookup for a particular selector failing for a particular object.  Calls to advice objects are triggered by the methods they are advising being called.  it's entirely different.  The similarity is in the interface provided  by each for handling method calls.  In one case it's a failed method call. In anothat case it's a successfull method call that we have defined as one we wish to intercept.

I was about to remove that comment after realizing my stupidity, but you were too fast for me!

