---
layout: page
title: HowToManageSharedSourcesAmongstProjects
---

I think this has come up before, and the answers are generally something like use CVS, use symbolic links, use hard links, add the files to Xcode but uncheck "Copy" etc.

This I think is generally too much work. E.g. when I want to use     printf all I do is     #include <stdio.h>.

Now I have a lot of sources in my own "shared source directory", and I want to be able to enable and disable them just as easily as with standard library functions.

So far I have made the include dir in my build directory a symbolic link to this repository of shared sources. This means that all the header-only based things can be used just like standard functions (macros, templates, inline functions etc.).

But those which have a source counterpart do not work.

I have thought about compiling them all into one object file which is default added to all my projects, but four problems with this approach, 1) code bloat (as the linker does not do dead code elimination AFAIK), 2) who recompile the sources when they have changed? (okay, that is probably not that hard to do manually, using a makefile or similar) and the worst one 3) some of the sources have C++ classes declared as static data and do work in the constructors, for example I have a debug system where I can just add DebugMenu.cc, and it will add an extra debug menu to my MainMenu(.nib), from where I can toggle the level of debug output etc. -- certainly I do not want that to happen by default with all new applications (especially not the non-Cocoa-based ones), which brings me to number 4) some of my code is pure C++, other is ObjectiveC++, so I would need to link my pure C++ programs with the Objective-C (and probably foundation) framework.
----
Xcode has project templates for BSD, Carbon, and Cocoa static libraries. Static libraries don't need to be shipped with your product (as I am sure you know) and can be the subject of dead code elimination optimizations. I would recommend two separate libraries, one for your straight C++ and the other for your Objective-C++ code. So you don't run into the problem of having to link in obj-c support (probably in a debug build where I don't think dead code elimination is performed) if it's not needed. Of course, one of your libraries can reference the other if need be.

