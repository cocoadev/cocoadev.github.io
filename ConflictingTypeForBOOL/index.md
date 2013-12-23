---
layout: page
title: ConflictingTypeForBOOL
---

I have a requirement to use X11 and ApplicationServices framework in a single  Application.

When I tried to compile, I m getting the error : conflicting types for 'BOOL'.


There is a conflicting typedefs in the files Xmd.h and objc.h for BOOL as 

typedef unsigned char  CARD8;
typedef CARD8		BOOL;   (<X11/Xmd.h>)

and 

typedef signed char		BOOL;    


Any way I can resolve this ?

Thank You.....


----
    
#define BOOL X11BOOL
#include <X11/Xmd.h>
#undef BOOL

