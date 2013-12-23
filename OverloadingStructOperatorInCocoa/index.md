---
layout: page
title: OverloadingStructOperatorInCocoa
---

Howdy, I'm trying to figure out if it is possible to overload a struct operator in Cocoa.
My development process has been hampered tremendously by non-linear error messages.
(By this I mean: compile, and the program runs, add one line of code, the program doesn't compile, remove that line of code, still doesn't compile
remove another line of code, still doesn't compile, remove a third line of code, program compiles, replace third line of code, program compiles, replace second line of code, program compiles.)

I know I shouldn't be writing member functions for structs at all, but if it possible to do, it is exactly what I need for some scientific computing that doesn't need to have accessor methods filling up my files & time.  But I want to power and ease of Cocoa when it comes to giving me my basic object functionality and polymorphism everywhere else.

I need to overload the operators so that my scientific code is more readable; if I dealt with the individual members of the struct the code would be impossible to read, the same goes for using NSNumber and its freakishly long method names.

Here is some of my code:

    
// file: "Vector.h"

typedef struct _Vector{
	double x;
	double y;
	double z;
}Vector;

// --------
// file: "VectorTestor.m"

#import "VectorTester.h"
#include "Vector.h"
@implementation VectorTester
-(void)awakeFromNib
{
	double a, b, c, d;
	a = 1.0;
	b = 1.0;
	c = 0.0;
	d = 0.5;

	Vector v;
	v.x = a;
	v.y = b;
	v.z = c;
	
	Vector vt;
	vt.x = 0.0;
	vt.y = 4.0;
	vt.z = 3.0;
	
	NSLog(@"vector is %f, %f, %f", v.x, v.y, v.z);
}
@end


Which seems to work fine.
But when I add an operator function, member or not, like this:
(to the Vector.h file, after the final ";")
const Vector operator +(const Vector& v, const Vector& k);
XCode says "syntax error before the '+' token"


The solution is to rename the file from "VectorTestor.m" to "VectorTestor.mm"
and then it works!

BTW, search for CPlusPlus on here to find out more.

----
C++ allows operator overloading, not so for Objective-C. Also, C++ classes and structs are essentially the same thing, and it's perfectly permissible (if a little unusual) to add methods to structs. Lucky for you, Objective-C++ lets you mix C++ and Objective-C code easily, and that's why changing the file extension from '.m' (Objective-C) to '.mm' (Objective-C++) solves your problem. Just remember that Obj-C classes and C++ classes/structs aren't interchangeable, so don't go trying, for example, to retain instances of Vector or to delete instances of VectorTestor. -CS

