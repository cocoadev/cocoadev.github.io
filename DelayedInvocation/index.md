---
layout: page
title: DelayedInvocation
---

Hello, I'm currently trying to implement a delayed invocation of a method that takes an id and a boolean, my code is as follows:

    NSMethodSignature * signature = [self methodSignatureForSelector:@selector(saveDocument:closeAfter:)];
			NSInvocation * invoke = [NSInvocation invocationWithMethodSignature:signature];
			[invoke setArgument:self atIndex:2];
			BOOL close = YES;
			[invoke setArgument:&close atIndex:3];
			[invoke setTarget:self];
			[NSTimer timerWithTimeInterval:0.5 invocation:invoke repeats:NO];

It looks like it should work to me, but nothing at all happens. Any ideas?

----

There's a couple of things wrong.

First, you didn't set the selector. Creating an invocation with a method signature does not do this, you have to do it as a separate step:

    
[invocation setSelector:@selector(saveDocument:closeAfter:)];


Second, you must always pass pointers to the argument, meaning that when you set     self as the second argument, you have to pass a pointer to it. Yes,     self is already a pointer, but since the pointer is the argument, you have to pass a pointer to the pointer, by passing     &self

