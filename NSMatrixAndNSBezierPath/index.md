---
layout: page
title: NSMatrixAndNSBezierPath
---

I get funky errors when I'm trying to compile a screensaver. Here is the content of the function (void)anmimateOneFrame :
    
- (void)animateOneFrame
{
    switch (c) {
    case 0:
        
        x = SSRandomIntBetween(0,a);
        y = SSRandomIntBetween(0,b);
        
        if (matrice[a][b] = 0) {
            c = 1;
            dx = SSRandomIntBetween(-1,2);
            if (x == 0) dy = SSRandomIntBetween(0,1)*2-1;
            if ((x == 1) || (x == -1)) dy = 0;
            if (x == 2) { x = 0; dy = SSRandomIntBetween(0,1)*2-1; }
            
        }
        else break;
    
    case 1:
        
        NSBezierPath *p = [NSBezierPath bezierPath];    // This is the supposedly wrong line
        p = [NSBezierPath bezierPath];
        [p moveToPoint:matrice[x][y]];
        [p lineToPoint:matrice[x+dx][y+dy]];
        
        x += dx;
        y += dy;
        
        
        
    }
}


I get this error :
    
parse error before **' token


And my second problem is about regular C matrices. I want to define my matrix with variable values, instead of arbitrary values. Is it possible ?

-- Trax

----
Hey Trax,

I think you might want to read up on how to bracket switch statements and look at the documentation for NSPoint, NSRect and NSSize.

    
-(void)animateOneFrame
{
    switch (c) {
        case 0: {
            
            x = SSRandomIntBetween(0,a);
            y = SSRandomIntBetween(0,b);
            
            if (matrice[a][b] = 0) {
                c = 1;
                dx = SSRandomIntBetween(-1,2);
                if (x == 0) dy = SSRandomIntBetween(0,1)*2-1;
                if ((x == 1) || (x == -1)) dy = 0;
                if (x == 2) { x = 0; dy = SSRandomIntBetween(0,1)*2-1; }
                
            }
            else break;
            
            break;
        }
        case 1: {
            


            NSBezierPath *p;    

           //  you were assigning a path twice, but that probably wasn't 
           // causing the parse error, I think it was how your brackets were done 
           // for the entire switch statement

            p = [NSBezierPath bezierPath];
            
            // part of your problem is that moveToPoint:(NSPoint) takes a struct of type NSPoint
            //
            //	typedef struct _NSPoint {
            //	    float x;
            //	    float y;
            //	} NSPoint;
            // 
            // I assume that matrice is some scaler value, but you want to 
            // draw a line based on two dimensional cordinates so I have
            // no clue what you are trying to do with these arguments
            // Here's a blind guess on what you might be trying to do
            //
            
            [p moveToPoint:NSMakePoint(matrice[x][y], matrice[x][y])];
            [p lineToPoint:NSMakePoint(matrice[dx][dy], matrice[dx][dy])];
            
            x += dx;
            y += dy;
            
            break;
        }
        default : break;
    }
}


----

oh, I guess matrice could be a 2D array of NSPoint's. Is it?

----

There's no particular reason that you need to put all the code for a particular case inside a single block, though I do agree that the fall-through from the first to the second case in the original code was probably not the desired behaviour.  I'm guessing that Trax recently installed the gcc 3.3 updater, and thus needs to run     sudo gcc_select 3 at the command line to set his default compiler back to 3.1.  The 2.95 compiler only allowed variable declarations at the beginning of a function. -- Bo

PS.  And please reload a page before you re-edit it to make sure you don't delete other people's replies.

PPS.  As for the second question, are you referring to creating a variable size array at runtime?  Because there's a gcc extension that allows you to dynamically specify the size of an array that I assume would work for multidimensional arrays as well.  See  file:///Developer/Documentation/DeveloperTools/gcc3/gcc/Variable-Length.html for details.

*I would have thought the blocks were there to make the line     NSBezierPath *p; valid ObjC - declarations must occur only at the start of blocks.*

There is a little discussion of this on the GCCThreePointThree.  The long and short of it is that the newest C standard, C99, finally allows variable declarations to be mixed freely with other expressions, and that gcc 3.1 and 3.3 support this idiom, while 2.95 does not.  I haven't used it for a while, but if memory serves, 2.95 did not allow local variables to be declared within arbitrary blocks but only at the start of the function.  -- Bo

