---
layout: page
title: CompilationParseErrorsRelatedToQuickTimeVersion
---

My app was compiling all right. Suddenly, just after updating to QuickTime 6.4 (I use 6.5 now and still doesn't work), when I tried to recompile my app, I got a bunch of cascading parse errors refering to the QuickTime Framework headers. 94 errors in all. All the cascading errors look like :
    
/System/Library/Frameworks/QuickTime.framework/Headers/ImageCompression.h:5009:
parse error before "AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER"


I tried to clean up the project, delete the build folder and recompile, delete the QT framework and re-add it to my project. Nothing works. If anyone knows how to alleviate this problem, let me know... -- Trax
----

What I'd do first is to copy and paste the compile line to the terminal, add a "-E", remove the "-o myfilename.o" and redirect the output to a file.  This casues the compiler to output what the C preprocessor has created.  Look in that file for the offending line - hopefully the error will be Obvious, and then you can back-track from there to see what caused it.

----

I get the same behaviour with Apple's sample code........ What's the problem ?

----

Doesn't this kind of thing also sometimes happen (in later versions) if the SDK is not installed?

----
Indeed - you may want to try the simplest fix: Reinstall XCode Tools.

