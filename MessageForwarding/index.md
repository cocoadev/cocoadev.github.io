---
layout: page
title: MessageForwarding
---



NSObject defines quite a few methods which are given default implementations that you can override if you choose to. For instance, the -forwardInvocation: method.

Because Objective-C uses DynamicBinding, you don't always know what object--or, because of DynamicTyping, even what *kind* of object--will be receiving any particular message. So, sometimes messages will be sent to objects that can't deal with them. Generally, you try to avoid these situations, but sometimes you can use them to your advantage.

When a first-class object (that is, an object which is a subclass of NSObject or which adopts the NSObject protocol) receives a message it doesn't respond to, it calls that object's -forwardInvocation: method. If you haven't overriden NSObject's -forwardInvocation, then NSObject's implementation is used, which just calls the -doesNotRecognizeSelector: method.

The -forwardInvocation: method is passed an NSInvocation which contains information including the arguments, selector, target, et caetera of a message. You can use that information to a couple of things:


* Forward the invocation on to another object, so that it can respond to it.
* Respond to the invocation yourself, which I'll go into a little later on.


You can use the -forwardInvocation: method to pass the invocation on to another object that will be able to do something with it. That allows you to have a single object appear to respond to methods of its class as well as methods of the class of the object which it is forwarding to. You can use this to simplify the programming interface of your program, if you wish.

You can also respond to the invocation yourself, but that takes rather more work. You have to override another method from NSObject, -methodSignatureForSelector:, and that is out of the scope of this page. You can find more information about that topic in Apple's pages:

*(hyperlink is on 2 lines)*

http://developer.apple.com/techpubs/macosx/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSObject.html

#//apple_ref/occ/instm/NSObject/forwardInvocation:

One last note, in the form of a suggested use for MessageForwarding; you could use it to log something to the console whenever a message your object doesn't respond to is called, thereby notifying you of what classes and instances are being used where.

Hopefully, this will be useful to you!

-- RobRix

----

"forwardInvocation:" is also useful to save a message to be sent at a later time. You can use this to mimic the NSUndoManager "prepareWithInvocationTarget:" method. -- RyanBates

----

The primary problem with message forwarding for general use is that it's slooooooooow (because it has to create an NSInvocation). I've been attempting to make a generic decorator object, which would "wrap" an object and forward messages to it while appearing as that object to the rest of the program (the idea is that subclasses of the decorator object would have added functionality, like resize handles on a view). Obviously using forwardInvocation would be rather slow, so I've been researching editing the method list of the decorator object to add all the methods of the decorated object (with it set up to call the methods on the decorated object, of course). No progress so far, but it's a fun problem. -- DavidSmith

