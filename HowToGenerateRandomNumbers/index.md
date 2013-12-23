---
layout: page
title: HowToGenerateRandomNumbers
---




To include random numbers in your program use the following methods.

Start by seeding the random number generator with the time.  You only need to do this one time when the program starts.
    
srandom(time(NULL));


Also see srandomdev(), which seeds the RNG state with something irreproducible.

Next, generate the random number using the following method...
    
random()


This method returns a signed long (0 - 0x7FFFFFFF) and you can tailor it to your needs by using the modulus (%) operator (although you shouldn't -- see below).

Here is an example of the random() method that generates a number from 1 to 100...
    
int generated;
generated = (random() % 100) + 1;


In this code, the number that is generated is divided by 100 and remainder is found.  The remainder could be anywhere between 0 and 99 in this case, so 1 is added to it to give it a range of 1 to 100.

This method is, however, not entirely satisfactory.  Because of the nature of most random number generators, use of the modulus (%) operator can drastically reduce randomness.  For that reason, the following example is preferred to the one given above:
    
int generated;
generated = (((float)random() / RAND_MAX) * 100) + 1;
if (generated > 100) generated = 0;

In this example, a number between 0 and 100 is generated, and we add one to it to bring it into the range of [1-101]. Since 101 is out of range, we look for that case and set it to 0. See RandomNumber if you want to know more about the details. Note that RAND_MAX must be declared as the maximum possible return value from random(). RAND_MAX is defined along with random() and srandom() in "stdlib.h"

Mac OS X's ScreenSaver framework contains some handy macros for generating random numbers with random(): SSRandomIntBetween and SSRandomFloatBetween. Instead of writing your own, you could just pull them out of ScreenSaverView.h. Don't forget to initialize the random seed.

----

From the disscussion in RandomNumber, and the man page, random() is safe to use with %, so the following is a valid function to return numbers between lower and upper, inclusive.  I still might use a Mersenne Twister with RAND_MAX, but this is a decent quick and dirty function. -- ScottEllsworth

    
+ (long)randomLongFrom:(long)lower to:(long)upper{
	return (random() % (upper-lower+1)) + lower;
}


----

See also: RandomNumber ScreenSaverView

Back to HowToProgramInOSX

