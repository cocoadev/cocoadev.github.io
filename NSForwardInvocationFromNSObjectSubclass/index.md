---
layout: page
title: NSForwardInvocationFromNSObjectSubclass
---

If I have a class that looks something like this:
    
@interface MyClass:NSObject {
     MyClassA *variableA;
     MyClassB *variableB;
}

- (void)someMethod;
@end


And **vriableA** responds to * - (void)someOtherMethod *, is it possible to send someOtherMethod to an instance of **MyClass** and have that instance forward the message to **variablA** given that **MyClass** inherits from NSObject, instead of NSProxy?

*Yes, just use the same method you would for a normal NSProxy subclass.*

----
So what's the difference between a class like *MyClass* that inherits from NSProxy, instead of NSObject (besides the major archiving and copying pain in the ass subclassing NSProxy provides)?

*
Forwarding with forwardInvocation only works with messages that MyClass doesn't respond to. You have to manually override the messages MyClass does respond to. If MyClass extends NSObject, that's a lot of messages to override. NSProxy has substantially fewer methods, so it's the better choice if you're forwarding all messages.

NSProxy also doesn't have a default implementation for potentially awkward methods like doesNotRecognizeSelector and methodForSelector. Those are the kind of messages you probably don't want your proxy responding to.
*

