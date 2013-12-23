---
layout: page
title: MissingLibFilesInApplesDevKit
---

I'm writting a Foundation framework program and wanted to use <stack.h> however it seems to not exist in the standard developer release.  Has Apple provided something to its equivalent? 

----

If you're referring to the STL header stack.h, it is obsolete.  You can find it with the C++ compatibility headers in /usr/include/gcc/darwin/g++-v3/backward or, preferably, switch to #include <stack>  -- Bo

