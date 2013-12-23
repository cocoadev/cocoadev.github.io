---
layout: page
title: ImportVsInclude
---

Objective-C files can include header files by using the standard C #include 
preprocessor directive or by using the Objective-C #import preprocessor directive. The #import 
directive is similar to the #include directive. #import assures that no file is imported 
more than once. Objective-C header files that are imported don�t need to be 
surrounded by �guards� (usually implemented with #define, #ifndef, and #endif) to 
keep them from being included multiple times. You should use the #import directive 
to import Cocoa headers because the Cocoa headers may not contain guards. 

What is #import ?

It's a C preprocessor construct to "include" the textual content of another file while automatically avoiding multiple inclusions of the same file.

    
#import <Object.h>


is an alternative to

    
#include <Object.h>


If #include is used, the included file must provide "guards" such as the following to prevent multiple inclusions:

    
#ifndef _OBJECT_H_
...
#define _OBJECT_H_
#endif

