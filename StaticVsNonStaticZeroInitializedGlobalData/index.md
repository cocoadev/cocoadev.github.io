---
layout: page
title: StaticVsNonStaticZeroInitializedGlobalData
---

Program 1:
    
#include <stdint.h>
static char array[10][1024][1024] = { };
int main () { return 0; }


Program 2:
    
#include <stdint.h>
char array[10][1024][1024] = { };
int main () { return 0; }


The first one produces an executable around 10 KB where the second produces one at 10 MB.

I was under the impression that     static in front of global variables only meant that the symbol for the variable would not be exported.  But clearly there are other differences.

If I remove the "= { }"-part, then both programs produce a 10 KB executable.

Both examples make the data global, why is only one of them stored in the executable? i.e.
    
static char array[10][1024][1024] = { }; // 10 KB executable
static char array[10][1024][1024];       // 10 KB executable
char array[10][1024][1024] = { };        // 10 MB executable
char array[10][1024][1024];              // 10 KB executable

All four are global variables. But the two first have local scope. They should all be zero-initialized. Why does the third cause 10 MB of zero-data to be stored in my executable?

----

As you've discovered, the memory for static vars is allocated at compile time, and persists throughout the life of the object (hence the keyword static.) Their scope is local, but their life is global. When you assign something to the array in your code, the 10MB will be allocated at runtime.
