---
layout: page
title: MultiplicationVsDivision
---

Hello there, I have heard that division is much slower than multiplication on the PPC, so here is my question would there be a speed difference between (borderL/2) and (borderL*.5) since they are both logically the same thing?

----

There definitely would be a difference, but I'm not sure which way it would go.  *.5 is a floating point operation, while /2 is an integer operation.  What's definitely true is that integer multiplication is faster than integer division.

----

If you're dividing by 2, couldn't you just borderL = borderL >> 1) ?

----

That's true, but I think the original poster's question is interesting in the general case.  What happens when you replace integer division with floating point multiplication?

*The time the calculation takes is increased, decreased or stays the same. Do I win the prize?*

----

Just for fun (revised)...
    
int i=0, tmp, val=100;
for (;i<10000;i++) tmp = val/2; // tmp = val*.5;
-------------
Division: 0.043s
Multiplication: 0.041s


Those are the fastest results after 3 runs each.

:I ran this test and got the same results. Howerver, noting that you don't init i, I tried to put i = 0; for some reason this didn't effect the division, but my results then were: Mult: 0.120s, Div 0.115s /EnglaBenny

*Interesting, that shouldn't have made a difference at all.*

----

I attempted to make a test but I don't know if it works correctly... (please excuse my ignorance)
    
#include <stdio.h>
#include <time.h>

int main()
{
	const int initialValue = 10;
	int i, j;

	clock_t start, end;
	double elapsed1=0.0, elapsed2=0.0, elapsed3=0.0;
	const int testRuns = 100;
	
	for (j=0; j<testRuns; j++)
	{
		start = clock();
		for (i=0; i<2000000; i++)
		{
			initialValue / 2;
		}
		end = clock();
		elapsed1 += ((double) (end - start)) / CLOCKS_PER_SEC;
	
	
		start = clock();
		for (i=0; i<2000000; i++)
		{
			initialValue * 0.5;
		}
		end = clock();
		elapsed2 += ((double) (end - start)) / CLOCKS_PER_SEC;

	
		start = clock();
		for (i=0; i<2000000; i++)
		{
			initialValue >> 1;
		}
		end = clock();
		elapsed3 += ((double) (end - start)) / CLOCKS_PER_SEC;
	}
	
	printf("elapsed1: %.16f\n", elapsed1/testRuns);
	printf("elapsed2: %.16f\n", elapsed2/testRuns);
	printf("elapsed3: %.16f\n", elapsed3/testRuns);
	
	return 0;
}


----

If you are dealing with integers and do     someValue / 2 then I'm quite sure you can count on the compiler actually doing     someValue >> 1, that's why division is much faster above (also, in the multiplication case it needs to convert to and from floating points in each iteration).

----

Agreed.  You need to do a different case.  I think the integer-to-floating-point conversion should be considered part of the cost though..

I'm a little surprised that the above loops compile to any code at all since they don't do anything.  Are you optimizing?  Aside from the need to make sure the compiler puts *something* in, we're probably interested in the optimized result.  Maybe?

I don't know how good clock() is, but I do know that mach_absolute_time() is the highest precision timer in Mac OS X.  There's a bit of a hoop to jump through to convert the units involved to seconds, but you can use the numbers as a relative measure.  Or, code to do the conversion is on the AlternativesToPrefixing page.

----

It should be noted that / 2 is *not* equivalent to >> 1 in all cases. In particular, with signed integers and negative numbers, >> 1 will round in the opposite direction. For example, -1 / 2 = 0, but -1 >> 1 = -1. This means that unless the compiler can guarantee that your number will always be positive (which is probably only going to happen if you explicitly declare it as unsigned), this optimization will not be made for you.

*Well, there are both signed and unsigned versions of the assembler shift instructions, but you cannot reach the signed version from C, though the compiler can still use it.*

The same is not true of multiplication. * 2 is always equivalent to << 1, so you can count on the compiler making this optimizations (and much more complicated shift+add substitutions for other constants).

*I'm silly; / 2 translates to >> 1, not >> 2. Fixed now, hope I didn't confuse anybody.*

----

The relevant compiler optimization is called "strength reduction". It replaces expensive instructions with equivalent cheaper ones (multiplies with shifts and/or adds for example).

----

As a corollary to above, you really shouldn't have to worry about this at all, because the compiler will nearly always chose the fastest path for you.  In the case of gcc, it will compute a "magic number" multiplier and perform a mulhw instruction (multiplies two 32-bit words and takes the upper 32-bits of the 64-bit result) with some shifting and multiplication and a subf in order to optimize division and/or modulus operations.

