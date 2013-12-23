---
layout: page
title: MemoryConsumptionForNSObject
---

Does anyone know how much memory an NSObject takes up?  An NSArray when empty?  How big is NSObject's struct and does anything else besides that struct suck up memory?

----

The first question should be easily answerable: use     sizeof(NSObject), assuming that doesn't cause compiler warnings. For the record, it's 4 bytes, or a single word, used for the     isa pointer to the NSObject class object. However, to this an extra 4 bytes must be added for the hidden cost of the retain count, and probably at least another 4 for bookkeeping costs in malloc (and quite plausibly 8, given common malloc schemes).

The question of NSArray is not so easily answered since the code is private, and moreover hidden behind both a class cluster and, doubtless, a pointer. You might have some luck creating an empty array and using zone commands to find the amount of memory it uses, though this ignores the possibility of extra memory hidden behind a pointer. Alternatively, you could use the sneaky method of allocating some memory, creating an NSArray, then allocating more memory, and hoping the three lumps are contiguous - which for a freshly-run application they may well be.

-- KritTer

----

If you don't need the information at run time (you are just curious) use the ObjectAlloc application which comes with the developer tools. It can tell you the size in bytes of any object in your program or any other program.

�Denis Elliott

