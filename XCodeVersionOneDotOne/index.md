---
layout: page
title: XCodeVersionOneDotOne
---



As cocoa-dev list subscribers undoubtedly are aware, unless you download the FULL Xcode Tools 1.1 package, you're only getting the Xcode IDE update, not the complete 1.1 developer tools. 

----

Whenever you create a new project the main.m file has a warning when compiled, this is easily fixed by changing main.m to the following (as found in the 10.4 version of General/XCode)...
    
int main(int argc, char *argv[])
{
    return General/NSApplicationMain(argc,  (const char **) argv);
}


----

It's a known bug in Xcode that has been rectified in 1.5. It only appeared in 1.1 or later if memory serves me correctly.

----

The default project code has errors in it.

----

Change the signature of main to { const char *argv[] }

----

From the Xcode Tools 1.1 Read Me:

FEATURES

The Xcode Tools 1.1 release provides overall stability and performance 
enhancements to the Xcode IDE, as well as improvements to debugging, 
workflow, the Xcode build system and General/CodeSense. It includes a number 
of revisions and additions not included in the recent Xcode 1.1 
software update; see the What's New section below. ...

WHAT'S NEW IN THIS RELEASE?

The Xcode Tools 1.1 release contains changes to a variety of tools, 
applications, and libraries in addition to those delivered in Xcode 
Update 1.1:

Development Tools

- General/InterfaceBuilder 2.4.1

- Linker and assembler

Performance Tools

- General/SpinControl

- CHUD (Computer Hardware Understanding Development tools)

Utilities

- General/FileMerge

Graphics Tools

- General/OpenGL General/DriverMonitor

- General/OpenGL Profiler

General/SDKs and libraries

- updated SDK packages for Mac OS X 10.2 and Mac OS X 10.3

- /usr/lib/crt1.o now included on the Xcode Tools CD

Examples

- New General/AppleScript Studio Examples

Documentation

- Numerous additions and revisions have been made to the developer 
documentation. ...

----

Anyone understand what kind of regex the Smart Group Inspector expects? The default for the implementation group is "?*\.[mcMC]", which is intended to catch all files with extensions such as .m, .c, .cpp, .cxx, .cc, etc. But clearly, it will match all files whose extension starts with m or c, regardless of case.

Anyway, I tried to modify it to include Python files, but I can't get things like "?*\.(m|py)$" to work (I only have General/ObjC and Python files in my project). Has anyone had success with this?

--General/PeterLindberg

----

Xcode docs say *a regular expression uses standard UNIX regular expression syntax. See the regexp(3) man page for a description of its syntax.* Wish I could help more, but I'm not enough of a regex guru.

**I guess that means that they're not Perl-compatible, but rather POSIX extended. I have no experience with the latter, so I can't help more either.**

General/XCode probably uses the same regex library as General/MOKit - General/MOKit's author is an Apple employee and much of the new Cocoa text stuff appears to be taken from it.
