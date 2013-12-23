---
layout: page
title: IBOutlet
---



Defined in <AppKit/NSNibDeclarations.h> as

    
#ifndef IBOutlet
#define IBOutlet
#endif


Unlike IBAction, which is defined as void, IBOutlet's sole purpose is to alert Interface Builder of outlets, and is thus an empty macro that is skipped by the compiler.

----

**Usage**

    
@interface className
{
 IBOutlet id outlet;
}
// ...
@end


----

See also IBAction.

