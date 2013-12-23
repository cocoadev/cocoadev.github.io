---
layout: page
title: ClassMethodsInThreadedEnv
---

Does anybody know how class methods work in a multi-threaded app?  If if have some method + [MyClass myMethod] and I call myMethod from thread A and at about the same time from thread B... what will happen?  

Why would it be any different than executing two instance methods? They will both run at the same time, conceptually, though in practice they won't on a single-proc machine.

Conceptually, with instance methods if you are running two instance methods on the same instance in two separate threads, issues of thread safety come up if you're writing to the member values.  In a class method, if there are no member values (unless you have static ones, which I don't in this case)... I'm wondering if the runtime system does something different for class methods.  

----

**I think you can think of class methods as special syntax for calling functions, no more.**

