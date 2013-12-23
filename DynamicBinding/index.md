---
layout: page
title: DynamicBinding
---



DynamicBinding is a system that puts off decisions about what method is being called until the program is running. This is done by deciding first what type an object is, and what method is being called.

It is the responsibility of ObjC's RunTime to determine which type of objects receive which methods.  In some other programming languages, these decisions are hardcoded at compile-time by the compiler.  The dynamic nature of ObjC makes it inherently more flexible, but with a performance hit that is often (but not always) negligible.

