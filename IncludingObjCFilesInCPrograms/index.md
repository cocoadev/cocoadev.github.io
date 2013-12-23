---
layout: page
title: IncludingObjCFilesInCPrograms
---

I understand how to include C files in Obj-C programs. But can I do the opposite and add Obj-C files into an existing C program. And if so, how? For my case, I want to add a geometry class to SQLite. I have developed the geometry objects and methods. Now how can I call these files and pass information in the form of C strings from the established SQLite code in C to the Obj-C files and back?

Here is an example:

Typical SQLite C

static int parseHhMmSs(const char *zDate, double *prJD)

Obj-C 

-(BOOL)parse:(NSString *)geometryText intoArray:(NSMutableArray *)pointsArray;

How can I call the Obj-C methods from within the SQLite C code (or an Objective-C method from any C code for that matter)? 

-Philip D Riggs

----

You should be able to declare the needed ObjC function in a header file and call them directly.      extern may be required when declaring the prototypes.
[Obviously, all code will need to be compiled with the same compiler.]

- BruceB

