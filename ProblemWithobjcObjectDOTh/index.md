---
layout: page
title: ProblemWithobjcObjectDOTh
---

I've recently upgraded my mac to Tiger ( Upgrade for Jag ) and am haveing a few dramas.

 I Don't know if Xcode2.0 has broken something but whenever i go to comile i get the error 

    
/usr/bin/ld: Undefined symbols:
.objc_class_name_Object
_objc_msgSend
collect2" ld returned 1 exit status


here is the code i'm trying to compile ( yes i'm just starting out with Ojective C )

Thanks In Advance.

    
#import <objc/Object.h>
#import <stdio.h>

@interface Point: Object
{
        int  x;
        int  y;
}

-(void) setX: (int) xVal;
-(void) setY: (int) yVal;

-(int) x;
-(int) y;
@end

@implementation Point;
-(void) setX: (int) xVal
{
        x = xVal;
}

-(void) setY: (int) yVal
{
        y = yVal;
}

-(int) x
{
        return x;
}

-(int) y
{
        return y;
}
@end

int main (int argc, char *argv[])
{
        Point *pt1, *pt2;

        pt1 = Point alloc] init];
        pt2 = [[Point alloc] init];

        // set first point to (100, 200)

        [pt1 setX: 100];
        [pt1 setY: 200];

        // set second point to (-20, 55)

        [pt2 setX: -20];
        [pt2 setY: 55];

        // retrieve and display values

        printf ("Pt1 = (%i, %i)\n", [pt1 x], [pt1 y]);
        printf ("Pt2 = (%i, %i)\n", [pt2 x], [pt2 y]);

        // release their memory
        [pt1 free];
        [pt2 free];

        return 0;
}


*Note to original poster; bracket your code in \\%\\%BEGINCODE\\%\\% ... \\%\\%ENDCODE\\%\\%, otherwise it's impossible to read. See [[TextFormattingRules.*

You probably need to link against libobjc by adding     -lobjc to your compiler flags. How are you compiling this code?

----

If you're just starting out with ObjC, you'd probably find it easier using the Cocoa NS* classes.


Thanks alot for your help, linking with -lobjc did the trick, compiles with no dramas now, now i can stop pulling my hair out.

