---
layout: page
title: ArrayOfNSStringsToCStrings
---

What would be the best way [or even any way] to do this?

Here is what I have so far, all I really know for sure is that it compiles and seems to do the job:

    
 static char ** convertPathArray(NSArray *files)
 {
     int i;
     int count = [files count];
     const char * cfiles[count];
     for (i=0; i < count; i++)
     {
         cfiles[i] = [[files objectAtIndex:i] fileSystemRepresentation];
     }
     return (char **)cfiles;
 }


----

uh, is that safe?  You're using the call stack for the cfiles array, and then returning that.  The address of cfiles gets returned, not the contents.  Whomever calls this now has a pointer into the stack which could get smashed.  Also, the docs say that the return value from cfiles 'will get freed' like autoreleased objects.  If you're going to be hanging on to them longer than the current event, you'll want to make a copy.  A safer way to do this would be to do something like this:

    
 static char ** convertPathArray(NSArray *files)
 {
     int i;
     int count = [files count];
     const char **cfiles = malloc (sizeof(char *) * count);
     for (i=0; i < count; i++) {
         cfiles[i] = strdup ([[files objectAtIndex:i] fileSystemRepresentation]);
     }
     return cfiles;
 }


And then when you're done, walk the array free()ing the strings, then free the cfiles pointer.

----
Thank you, I figured that compile warning was not just GCC pulling my leg :) And I didn't know how to fix it. (returning address error)

Yes, it's good to trust the compiler warnings :-)

