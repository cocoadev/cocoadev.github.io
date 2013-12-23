---
layout: page
title: HowToDebug
---

** Enable debugging **

There are several things you can do to make your project more debugable.

* Generate debuging symbols - in the Targets tab, click on your target.  Click "GCC Compiler Settings".  Make sure "Generate debugging symbols" is checked.
* Disable optimization -  in the Targets tab, click on your target.  Click "GCC Compiler Settings".  Set "Optimization Level" to "No optimization".
* Break on exceptions - in the Breakpoints tab, click New.  Add the following text "objc_exception_throw"


----
Once you know how to debug your code you may appreciate some DebuggingTechniques.

Here are some ErrorsAndWarnings you might run into.

