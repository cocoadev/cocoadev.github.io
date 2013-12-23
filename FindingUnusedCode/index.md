---
layout: page
title: FindingUnusedCode
---

Does anybody know a good way in Xcode (or otherwise) to find all the functions and methods in a project that aren't called from anywhere in the code.  It would be nice to have a clean way of shaving off the extra flab.
----
A deployment (release) build will perform dead code elimination. Considering the dynamic nature of Objective-C/C++ I am not sure if this applies to those method entries as well, it may if they are defined within the application and not part of a framework.
----
It can't be done with methods.

Consider something very simple like:     [NSClassFromString(aString) performSelector:NSSelectorFromString(bString)]. There is no way for the compiler to know if such code may be present in your application at runtime. Even if it could analyze your entire app, it can't analyze the frameworks you're linking against, or input managers your app loads at runtime, etc. And while most of the scenarios where your code could get referenced like that don't matter, the compiler simply can't know. You could make a tool that gave you a list of methods that aren't directly referenced in a method call, and then lets you decide whether those methods are required or not, but I don't know of such a tool.

----

For regular functions, if they're file scope, making them static can be a clue to the compiler that they can be stripped out if not used.  Otherwise, the linker should be dead-stripping functions that aren't accessed.

----

Half the issue for me isn't the app footprint, but the actual code base and it's readability/maintainability.  Do any of you see a clean AppleScript way of finding methods and functions that aren't directly called?

