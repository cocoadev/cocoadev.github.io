---
layout: page
title: PlusAndMinusMethod
---

The flavor of ObjectOrientedProgramming implemented by ObjectiveC includes two types of objects: the ClassObject and the InstanceObject.

see also MethodSignature

*The above statement is somewhat confusing and, IMO, "wrong". A "class" is a blueprint for an object, while an instance is an actual "instance" of that blueprint (the difference between the plans for a type of car and actual cars built from that blueprint). For someone who honestly doesn't understand what + and - signify, they may not "get" the above sentence. Although, asking a "blueprint" to give you something or do something for you isn't exactly intuitive either ... :-)*

----

Yeah, I getcha there. "So I'm, like, how can a CLASS be the receiver of a message?" Maybe we don't want to be telling the newbie asking this question about those niceties. Hmm? And yet we have those fine pages referred to above. Except for the reference to MethodSignature, that confusing remark could go. I think the sooner a newbie gets a hint about just *how* much a Class incorporates, the better.

*To quote ABY, p. 18 (BookCocoaProgramming): "Classes are objects that describe instances." It does not get more succinct than that.*

Hey, if ObjectOrientedProgramming was intuitive, there would be more programmers than there are help desk reps. I don't think confusion is preventable, except occasionally among those who were actually *born* programmers. There are elite programmers and journeyman programmers. Each of you reading this sentence knows which group you fall into.

----

A method starting with a plus (like     +sharedWhatever) is a ClassMethod. A method starting with a minus (like     -init) is an InstanceMethod.

When invoking a ClassMethod, the receiver of the message must be a class, such as NSArray, NSString, or a class that you define. When invoking an InstanceMethod, the receiver of the message must be an instance of a class, such as that returned by sending an     alloc message to a class, or one returned by invoking a FactoryMethod.

You should not use an InstanceVariable in a ClassMethod (that much should be obvious, since the ClassObject exists via the RunTime, without any instances of it having been created.) See ClassMethodsAndInstanceVariables.

For a more in-depth treatment of the PlusAndMinusMethod syntax, read the discussion of InstanceObject.

