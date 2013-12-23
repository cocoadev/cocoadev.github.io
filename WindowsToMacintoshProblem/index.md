---
layout: page
title: WindowsToMacintoshProblem
---

I am currently creating a Macintosh version of an existing Windows application. The Windows version was implemented using Visual C++. I am trying to create the Macintosh version using Objective C. I am not quite experienced with Macintosh programming especially Objective C and it seems that C++ operators like new and delete (especially the array []delete operator) are not applicable in Objective C. Can this really not be used? 
Also I would like to ask for some advice as to how to do with the entire Windows to Macintosh project. I would welcome any advice with regards to this. Thanks so much!!!

----

Moving a project from C++ to Objective-C, you most likely want Objective-C++. Rename your .m files to .mm.

Is the Windows app your moving WIN32 SDK, ATL/WTL or MFC?
