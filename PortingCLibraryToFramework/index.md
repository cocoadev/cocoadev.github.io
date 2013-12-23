---
layout: page
title: PortingCLibraryToFramework
---

I ported a free C library to Mac OS X (actually I just changed one word on the libtool file). I can use it from a cocoa application, but I want to wrap this library in an Objective C class, and create a reusable code that I can publish as free software.

The library is buit like most unix tools in a very very complicated process, using 4 auto configuration systems. You know - /.configure make etc. 

Can I covert it into a Project Builder framework project, or I should build a framework which use the compiled library? I guess I want the library to live inside the framework, so anyone can install and use it without an installer. --NirSoffer

----

It'd probably be easiest to link your framework to the static library.  One possible hitch: if there's a dynamic library with the same name (except the .a vs. .dylib extensions) as the static library, PB will silently use the dynamic one even if you directly include the static one.  The best technique I've discovered is to just copy the static version into your project folder and include it from there. -- Bo

Are librarys you include from the project folder copied into the target framework?

PS  I believe you can use ./configure --disable-shared in the make process to only make the static libraries.

