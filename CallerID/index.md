---
layout: page
title: CallerID
---

Does anyone know how to figure out who's calling you.  So:  for instance

- (void)methodA
{
   // some code that does something
   [self methodB];
}

- (void)methodB
{
  // some code
  NSLog(@"The caller is:%@:, [someFunctionThatWillGiveMeTheSelector(someArge) NSStringFromMethod);
}


Desired Output: "The caller is:methodA

Is this possible, without explicitely passing methodA's selector to methodB as an argument?

----

The general case would be pretty hard, especially since your method could be called from a C function and not have a selector.  There is some information on StackTraces that you may be able to adapt for this purpose.  You can also get the stack frame address with NSFrameAddress (found in NSDebug.h) and grovel around inside of that.  I doubt it would be very robust in the long-term (compiler changes, etc).   What are you wanting to accomplish?  ++MarkDalrymple

----

I don't think the built-in modem in macs supports caller-ID - DiggoryLaycock

You're joking, DiggoryLaycock... right?  If you weren't, then CallerID was a "joke" in and of itself -- the question was asking if it's possible to know what where a function call came from, if you're the function being called.

