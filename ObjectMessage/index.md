---
layout: page
title: ObjectMessage
---



An ObjectMessage is sent to AnObject and consists of the ReceiverObject (someObject in the following example), the InstanceMethod that is being called (doSomethingWithObject:andAnotherObject: in the example), and any arguments that are passed to it (theFirstObject and theSecondObject in the example). 

    
[someObject doSomethingWithObject: theFirstObject
                 andAnotherObject: theSecondObject];


usually just called "Message", but we can't make a link out of that and MessAge is just too, uh, mess-y

And, in fact, you can send an ObjectMessage to a ClassObject, since a class is an object in our neck of the woods.

see also MethodSignature

see also SeLector

