---
layout: page
title: PassAnArrayFromAFunction
---

You can't return an array in C. Assuming you don't want to use an NSArray with its associated enormous (for this sort of task) overhead, you have to pass the array into the method by reference:

    
- (void)methodWithScalar:(float)s array:(float [6])inArray output:(float [6])outArray
{
     ...do stuff and write to outArray...
}


C arrays are always passed by reference, so any modification you make to the parameters will show up in the caller. You can also return a pointer from a function, but see also HowToUsePointers, WhatToKnowAboutCFunctions, all in HowToProgramInOSX

----
Really? You can really say     float[6]? I always thought you had to just use     float *. --JediKnil

