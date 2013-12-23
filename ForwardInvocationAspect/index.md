---
layout: page
title: ForwardInvocationAspect
---

ForwardInvocationAspect - just an idea for AspectCocoa

So far we've been replacing methods with replacementIMPs... what if we instead completely drop methods we want to replace and override forwardInvocation: on all classes we're applying an Aspect to. we'd also have to override methodSignatureForSelector:.  So we'd change forwardInvocation to access a lookup for the class we've removed. (if not found, proceed with the normal forwardInvocation).  We'd change methodSignature to access our lookup and return the signature we've store for the method prior to swapping it out.  We can make swapping in and out easier by renaming rather than removing methods.

So it goes..
An object with an Aspect on it receives a message corresponding to a method it should have an Aspect on.
The class's method's name has been changed, so it is not found
methodSignatureForSelector on that class returns the proper signature from our lookup
forwardInvocation on that class receives an NSInvocation.
all before's are called.
The NSInvocation is modified to point to the renamed method.
all afters are called.
the return value of the invocation is passed back to the sender.


The problem is that NSObjects documentation says that the return value of the invocation is passed directly back to the original sender.  We have no idea how this is done. so we need to learn how.  Because we want to call our after advice after that value is returned...*"I don't think you want to call the after advice after the value is returned to the caller, but after the value has been calculated, right? If so, read below for my two cents"* CharlesParnot

our replacement forward invocation might look something like this

    

- (void)forwardInvocation:(NSInvocation *)invocation{
    id adviceList = [sharedLookup getadviceListForSelector: [invocation selector] onObject: self];
    [adviceList invokeBefores: invocation];

    //change the invocation selector to the other one...
    [invocation invokeWithTarget: self];
    //change the invocation selector back to what it was

    //call the afters, but without the return value of the invocation?
    [adviceList invokeAfters: invocation];

    //call the original forwardInvocation if we don't find advice for the invocation
}



we would also have to override respondsToSelector to make sure the class still reports that it will respond to the methods we've renamed...

ohh and make sure they still follow formal protocols...


----
I don't understand why you have a problem with the invocation. Once you invoke the invocation, the return value is stored in the NSInvocation object. You can get it using
    - (void)getReturnValue:(void *)buffer
The documentation of that function explains very well how to get the return value.

In fact, once you have called '[invocation invokeWithTarget: self]', you have the return value waiting for you, stored in the NSInvocation. What happens next? I think after forwardInvocation: returns, the runtime system simply returns the return value, something like this:
    
id objc_msgSend(id theReceiver, SEL theSelector, ...)
//OR id objc_msgSendSuper
{
    ...look for the IMP for theSelector... 
    //do this if cannot find the method for the receiver
    //create the NSInvocation
    NSInvocation *theInvocation;
    ... some code to create it ...
    [theReceiver forwardInvocation:theInvocation];
    id returnedObject;
    return [theInvocation getReturnValue:&anObject];
}


For other return types, '<code>void objc_msgSend_stret</code>' or '<code>void objc_msgSendSuper_stret</code>' is called, something like this would be used instead:
    
unsigned int length = myInvocation methodSignature] methodReturnLength];
buffer = malloc(length);
[invocation getReturnValue:buffer];
return *buffer;


In conclusion, it seems to be an EXCELLENT idea to use the forwarding mechanism. This will be completely independent of the implementation, and you don't have to care about method signature and such. In fact, you may not need to overwrite the forwardInvocation: for all the classes, you can use the same IMP for all of them!!! So you create the method in your class, and use the IMP for all the classes. The [[NSInvocation contains all the information you need.

