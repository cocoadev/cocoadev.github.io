---
layout: page
title: ProblemDoingCrossDevelopment
---

I'm running through Step Into Xcode, and am in the chapter on cross development.
I'm trying to build a 10.2.8 application on an Intel 10.4.7 machine running Xcode 
2.3.  

I get this error: "gcc-3.3: installation problem, cannot exec `cc1obj': No such file or directory"

It seems that gcc-3.3 didn't install properly, but I'm not sure what to look for or how to 
do about it.

----
Make sure you set your architecture to ppc. If you try to build i386 using gcc3, then Xcode will search for the i386 version of gcc3 which does not exist on OS X, and you'll get the error you describe. Intel builds can only be done with gcc4.

