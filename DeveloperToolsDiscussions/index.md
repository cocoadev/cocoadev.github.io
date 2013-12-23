---
layout: page
title: DeveloperToolsDiscussions
---



[Topic]

----

*I nominate this area as next for an overhaul. It's pretty messy. How about a DeveloperToolsDiscussions umbrella page, with  sections for XCode, InterfaceBuilder, PropertyListEditor, ProjectBuilder, IconComposer, and so on. I'll do some work on it tomorrow if I have time.*

**Done**

There's some docs on using XCode for Pascal development at [http://www.surtec.com/~rj/Xcode-FreePascal/]

----

Anyone got any idea how I can make XCode 2.0 autocomplete instance vars etc etc like it used to, instead of just methods like it does now? Its lovely and quick now but I miss that feature!

Wait till it underlines the partial word and hit Ctrl-. If the first suggestion is not the correct one keep using Ctrl-. till it finds the one you want.

----

IBM has released C, C++, and Fortran compilers for the G4 and G5.  They are supposed to produce very good code.
See [http://www-3.ibm.com/software/awdtools/ccompilers/] for more information.  It's a 55 MB download and 
requires registration.

From the ReadMe file:
    
Prerequisites
=============

Operating system: Mac OS X version 10.2

Hardware: Power Mac G5

Required software: The Mac OS X Developer Tools package, which 
includes gcc version 3.3 (December 2002 Mac OS X Developer Tools 
and Dec 2002 gcc Updater)


A G5 is required!  Guess it'll be awhile :(

I installed it anyway.  It installed!  I thought it might check to see if I was using a G5 and stop when it didn't
find one in my TiBook.

Compiling is another matter.  C worked.  C++ failed.  Could be something wrong in what I did....

It's not required actually - I've run it just fine on my G4. I think they might've meant that it's capable of generating code optimized for the G5 when you specify the options     -qarch=g5 -qtune=g5.

IBM's xlc has traditionally been a great compiler which (when you intelligently tweak the optimization settings) will generate some of the best PowerPC code out there. Of the command-line PPC compilers out there, the pecking order is probably xlc > MrC > mwcppc > gcc. Then again, MrC isn't being maintained any more and most people probably don't have the patience to run it in MPW, so you can probably disregard it.

----

Does this compiler support Objective-C, or is there reason to think that it will support Objective-C in the future?  This is very cool, but I can't tell if it's going to make any difference to us, either as as cocoa programmers or as Mac OS X users.  Apple can't even recompile a lot of the OS with it if it doesn't support Objective-C, right?

**AFAIK, it doesn't support it right now, but it's binary-compatible with GCC, so for our own projects we can code some stuff as C functions and link 'em in, if we care that much. Hopefully it'll be better integrated eventually.**

----

To link as C++ one needs to invoke the compiler as xlC (uppercase C), unfortunately with HFS, one cannot have the same file in different case, so probably the symbolic links were lost during install (didn't IBM test this?), but one can still invoke the compiler as xlC, just provide the full path.

Strangely though, I cannot get it to build Hello.cc, I get internal compiler error. But I did manage to make it build another project of mine. Unfortunately if I build it with optimizations my program will become unstable, both bus error, segmentation fault and even "invalid instruction" were among the errors I got when running the program (it would start fine, but some features of my program would trigger this) -- I did specify "-qarch=ppcv" for "Generic PPC with AltiVec" -- also, on optimisation levels other than 2, the compiler would bail out, clamming it couldn't inline a function.

So unfortunately I am not able to provide any real benchmarks, but compile time is definitely better (at least w/o optimisations), here is the output from "time" running the two compilers:
    
GCC: 7.050u 1.170s 0:09.80 83.8%     0+0k 0+35io 0pf+0w
XLC: 3.300u 0.970s 0:05.51 77.4%     0+0k 0+6io 0pf+0w

And executing the resulting file yields:
    
GCC: 7.450u 0.030s 0:08.43 88.7%     0+0k 0+0io 0pf+0w
XLC: 4.480u 0.060s 0:05.43 83.6%     0+0k 0+2io 0pf+0w


**But** the executable produced by xlc would appear to be buggy, as if my program is given one option then it hangs, but the result produced by the above run appears to be correct.

If the executable is optimised with -Os (which takes 12.6s) then the task completes in 1.75s.

This is all done using gcc 3.3, as 2.x can't compile my code, and 3.1 gives an internal compiler error on -O2 or above -- it would seem I have a tendency to write code which makes compilers fail, during the last month I think I have reported 4 bugs on gcc...

