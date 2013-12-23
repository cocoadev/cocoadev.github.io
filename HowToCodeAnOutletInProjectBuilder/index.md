---
layout: page
title: HowToCodeAnOutletInProjectBuilder
---

To add an outlet to your class in Project Builder, make sure you have a class and are in Project Builder.  Open up the header file for your class (Foo.h).  Now, add the following code in between the braces that follow the "@interface" line.
    
IBOutlet id myOutlet;

Instead of "id" you can put the type of object you are connecting to.  For example, NSTextField, NSButton, MyView, etc.  If you know what kind of class the outlet is hooking up to, it is good to code that in instead of leaving the "id" there.  "myOutlet" is the name of your outlet.

Back to HowToProgramInOSX

