---
layout: page
title: UsingAlternateAllocators
---

This is just a question out of curiousity, not a request for assistance. I generally use malloc()/free() when I need blocks of memory that aren't objects, but what do other people use? Does anyone use General/NSZoneMalloc(), General/CFAllocatorAllocate(), or (Carbon represent!) General/NewPtr(), and if so, why?
----
I generally use General/NSMutableData.  Just like malloc(), General/NSMutableData will provide a void pointer that I can cast to the type I want to actually store, � mutableBytes.  General/NSMutableData also provides an automatic realloc() capability, -increaseLengthBy:.  General/NSMutableData can provide small chuncks and huge chunks equally well.  Best of all, using General/NSMutableData lets me use Cocoa's memory management conventions unbiquitously.  I never have to worry about free() or who should free memory allocated within a function call etc.
