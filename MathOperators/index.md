---
layout: page
title: MathOperators
---

How can I find the remainder of the division of two numbers?

(I'm just starting to learn Cocoa after using REALbasic for years... Just about 100% of the documentation provided assumes the reader knows c/c++ inside and out. A C reference manual said % would do it, as in num1 % num2 = remainder, but Project Builder didn't like that)

You gotta switch that around: remainder = num1 % num2;

Note:  the value of remainder will be between 0 and (num2 - 1).

----

This only works for integers, or if you can do it with floating-point numbers, there's some trickery you have to use that I've not figured out yet. For floats and doubles, type "man ieee" (less the quotes) in the terminal, and check out remainder()/remainderf() -- RobRix

----

No trickery, you just need to use one of the standard math functions, fmod().

man fmod:

NAME
fmod - floating-point remainder value function 

SYNOPSIS

#include <math.h>

double fmod(double x, double y);

DESCRIPTION
The fmod() function returns the floating-point remainder of the division of x by y. 

----

Ah, very interesting.  Question:  Is there an index somewhere with descriptions of all the common C functions for someone like me to look at, and see if anything's useful?  There is, for example, an index of the cocoa objects and classes and so forth at apple.com... i'm trying to think of something similar for plain ol' C

--CharlieMiller
 
man math  

lists quite a few (all?) math functions available.  Other than that, get a copy of K&R (Kernighan and Ritchie), The C Programming Language.  Look at <math.h> and other header files, e.g., <stdlib.h>.   -- BruceB

----

Well, just look in /usr/include -- here you have a lot of .h files, 15 of these (IIRC) are part of the ANSI-C standard, and documentation can be found simply by typing (in a terminal) "man 3 <header>", e.g.


* man 3 ctype
* man 3 dirent
* man 3 math
* man 3 regex
* man 3 stdarg
* man 3 stdio
* man 3 string
* man 3 ...


For STL there is some online documentation, amongst others http://www.sgi.com/tech/stl/

--AllanOdgaard

----

I'll second the direction towards /usr/include. They're definitely useful to poke around in; I'm personally quite fond of /usr/include/objc.

-- RobRix

