---
layout: page
title: CocoaArchitecture
---

What is the common way to work with global app collections. Say you're writing Mail.app and you want to have a global set of servers that all the commands go through. How is that usually done? [[NSApp delegate] servers]? That would give warnings everytime you try and use it because [NSApp delegate] returns a generic object pointer. Hopefully this page will discuss some other common Cocoa architecture ideas as well.

----

SingletonDesignPattern is a popular way to get global access to objects in Cocoa.

----

You could also have a controller object instantiated in a nib file own an instance of your server class and provide access to it through an accessor method.

