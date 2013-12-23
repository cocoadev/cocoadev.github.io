---
layout: page
title: AnApplication
---



Applications are the grouping of compiled, executable code and data files in a bundle given the .app filename extension. When you write a program in Cocoa, you are writing AnApplication.

----

...unless you're writing aTool, which is really still AnApplication but not according to the ProjectBuilder template.

Tools typically aren't in a bundle - they're just a standalone executable in your build directory rather than a bundle folder hierarchy.  Plus applications (usually) have a UI, and tools don't. If tools are not applications though, then it's not always true that when you write a program in Cocoa, you are writing AnApplication.

