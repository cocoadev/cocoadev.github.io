---
layout: page
title: AltiVecAndSixteenByteAlignment
---

I want to use the AltiVec function for performing a scalar product (dot product) of two vectors declared:
<code>
extern float vSdot(
   SInt32 n,
   const vector float x[],
   const vector float y[]);
</code>

I am trying test the dot product function with a data type is declared as:
<code>
typedef struct {
	float	real;
	float	imag;
	float	dummy[6];
} imaginary;
</code>

    
<code>
/**********************************************'/
unsigned int avec_iterate2(imaginary *initVal, imaginary *constant, float escapeMagSquared, unsigned int escapeIterations)
/**********************************************'/
{
imaginary		val, val1, val2;
vFloat			*pVal1, *pVal2;
float			tempReal;
imaginary	temp1, temp2;

pVal1 = (vFloat   *)&val1;
pVal2 = (vFloat   *)&val2;
	
val1.real		= (float)100.0;
val1.imag		= (float)100.0;
val1.dummy[0]	= (float)100.0;
val1.dummy[1]	= (float)100.0;
val1.dummy[2]	= (float)0.0;
val1.dummy[3]	= (float)0.0;
val1.dummy[4]	= (float)0.0;
val1.dummy[5]	= (float)0.0;

val2.real		= (float)1.0; 
val2.imag		= (float)1.0;
val2.dummy[0]	= (float)1.0;
val2.dummy[1]	= (float)1.0;
val2.dummy[2]	= (float)0.0;
val2.dummy[3]	= (float)0.0;
val2.dummy[4]	= (float)0.0;
val2.dummy[5]	= (float)0.0;


temp2.real = vSdot(4, pVal1, pVal2); // Why is the result stored here 300? //
temp2.imag = 2*tempReal*val.imag + constant->imag ; 

return(temp2.real);
}
</code>


Thanks for any assistance.

WayneContello


----

You are running into trouble because your input data must be 16-byte aligned, but is not guaranteed to be the way it is written (casting your struct into a vFloat).  Try something like:
<code>
typedef union {
       vFloat vectors[2];
       struct {
              float	real;
              float	imag;
              float	dummy[6];
	} elements;
} imaginary;
</code>
--LN

The issue is that all Altivec functions require 16-byte alignment, which is not guaranteed when creating a struct in the manner that you do.  However, if you use the union type that I declared above, GCC is smart enough to figure out that you are going to be using vectors, and will byte-align for you.  Alternately, you can use malloc() get correctly byte-aligned allocations.  Here's a quick test which shows the correct result, 400.0.  To prove this is working, just compile it like: gcc test.m -faltivec -framework Cocoa -framework Accelerate

    
#include <Cocoa/Cocoa.h>
#include <Accelerate/Accelerate.h>

typedef union {
       vFloat vectors[2];
       struct {
              float	real;
              float	imag;
              float	dummy[6];
	} elements;
} imaginary;

int main()
{
	imaginary x;
	imaginary y;

	x.elements.real		= (float)100.0;
	x.elements.imag		= (float)100.0;
	x.elements.dummy[0]	= (float)100.0;
	x.elements.dummy[1]	= (float)100.0;
	x.elements.dummy[2]	= (float)0.0;
	x.elements.dummy[3]	= (float)0.0;
	x.elements.dummy[4]	= (float)0.0;
	x.elements.dummy[5]	= (float)0.0;

	y.elements.real		= (float)1.0; 
	y.elements.imag		= (float)1.0;
	y.elements.dummy[0]	= (float)1.0;
	y.elements.dummy[1]	= (float)1.0;
	y.elements.dummy[2]	= (float)0.0;
	y.elements.dummy[3]	= (float)0.0;
	y.elements.dummy[4]	= (float)0.0;
	y.elements.dummy[5]	= (float)0.0;
	printf("%f\n", vSdot(4, x.vectors, y.vectors));

	return 0;
}



--LN

for more discussion see AltivecOptimization in which this point is mentioned peripherally

----

I assume you are planning on using Altivec for multiplying *sequences* of complex numbers, because otherwise the latency of getting stuff into + out of the Altivec unit is going to totally negate any benefit over just using <code>x.elements.real*y.elements.real + x.elements.imag*y.elements.imag</code> - not to mention the horrors of maintenance you're invoking :)

A quick suggestion that may prove useful when you move from this toy code to something that really uses the power of AltiVec: if you're not sure whether your input arrays are aligned or not, just do the start and end elements the non-AltiVec way and only start AltiVec up at the first 16-byte-aligned position.

