---
layout: page
title: NSApp
---

NSApp is a global variable that points to the instance of your NSApplication.

It is initialized in the call to NSRunApplication() that is installed in your main() function by default.

NSApp is of type NSApplication, or of whatever you set as your project's Principal Class.

In rare cases, NSApplication should be overridden, but most often there are other ways to solve the problem, using the App's delegate.

----

The NSApplication instance can also be retrieved with     [NSApplication sharedApplication]

----

Wondering why the linker is complaining about an "unresolved external reference to _NSApp"? Probably because Cocoa wasn't included as a framework.

