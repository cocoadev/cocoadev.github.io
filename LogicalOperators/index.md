---
layout: page
title: LogicalOperators
---

Here is a partial list of logical operators. They are fundamental to conditional operations (if/else if/switch) and loops. They either return true or false (a boolean value), nothing else.


* ! (not)
* && (logical AND)
* || (logical OR)


Bitwise operations act pairwise on each corresponding bit of their two arguments. For example, 0xF0 ^ 0xAA will return 0x5A: 0xF0 is 11110000 in binary; 0xAA is 10101010; so 11110000 ^ 10101010 is 01011010 or 0x5A.

* ^ (Exclusive OR)
* & (bitwise AND)
* | (bitwise OR)
* ~ (bitwise NOT)


Relational operators compare two values and evaluate to a boolean value:

* == (equal to) - do not confuse with = (assignment). If you do, expect interesting results.
* != (not equal to)
* < (less than)
* > (greater than)
* <= (less than or equal to)
* >= (greater than or equal to)
 

You will use these and the many others extremely heavily.

The logical AND and OR operators short-circuit.  If the operation on the left side can unabiguously determine the value (false for &&, true for ||), then the right side isn't evaluated.

For example
     if ( (x != 0) && (y / x == 12) ) {
   ... whatever
} 

is safe to do - you won't get a division by zero.  If x is zero, the first clause of the if is False.  Since False AND anything is false, the rest of the if doesn't need to be evaluated.
----

Just in time, something that got me as I started:
To check if two NSStrings are equal, use isEqualToString:@"Text" or isEqualToString:YourStringHere , because the "==" and "=" won't work with them. - Nando

Since we are working with pointers to objects in Objective-C, comparing two 'objects' using "==" will actually compare the pointers. That might work if you want to know if both variables point to the exact same object. But most of the time what you really want to know if the *values* of both objects are equal. For those cases many classes implement isEqualTo: or similar methods. See also the NSComparisonMethods informal protocol: file:///Developer/Documentation/Cocoa/Reference/Foundation/ObjC_classic/Protocols/NSComparisonMethods.html

"=" on the other hand is not a comparison but the assignment operator. One pitfall is that in C an assignment will also return a value, so if you write "=" instead of "==" you may not catch your error immediately. Recent compilers - like the GCC version shipping with Apples DevTools - will issue a warning, though. --AndreasMayer

----

The EXCLUSIVE OR is not a boolean operator.  It is a bitwise operation.  Try this.
    
NSLog(@"0xF0 ^ 0xAA = 0x%02X", (0xF0 ^ 0xAA));


You will get '0xF0 ^ 0xAA = 0x5A'.

See ProtectingResources for a brief description of how you can use XOR to "encrypt" stuff. -- MikeTrent

----

**Discussion of short-circuiting in logical expressions**

Whenever you have an && or ||, the operands are evaluated left-to-right, and only enough of them are evaluated to give a definitive answer. In the case of &&, evaluation stops as soon as you get a 0, and in the case of || evaluation stops as soon as you get a non-zero. That means that it's perfectly safe to write code like this:
    
if(myStruct != NULL && myStruct->thingy > 3) ...act on myStruct...
if(myStruct == NULL || myStruct->internalPointer == NULL) ...flag an error...

Note that this is not a convenient loophole or taking advantage of quirks in the compiler; it is a part of the C standard and every conforming C compiler will follow it.
----

Regarding short-circuiting (in the unlikely case anyone reading this is missing the point) this means there's a opportunity to optimize a program by choosing the order in which conditionals are listed. This is more obvious if you look at something like (in pseudo-code):
    
  if (<<100 mb file successfully downloaded>> && <<it's February 29th>>) then ...

compared to:
    
  if (<<it's February 29th>> && <<100 mb file successfully downloaded>>) then ...


In general, you want logical "ands" arranged in *most likely to be false* order (as in the second example above) and logical "ors" in *most likely to be true* order. It obviously doesn't matter much if the conditionals don't require much processing, but can make a huge difference when one or more of them do.
----

Even if it does make it faster, don't forget, "PrematureOptimization is the root of all evil". Write your conditionals in the order which makes the most *logical* sense. *If*, at a later time, you discover via the use of a profiler that you're spending a lot of time executing a particular if statement, only then should you think about rearranging it to make it faster. This is not likely to make any kind of noticeable difference 99.999% of the time. If you spend an hour of your time making your program run 0.01% faster, it's wasted.

Your "sort order" should be "least expensive to process and most likely to be false" first and "most expensive and most likely to be true" last. This doesn't apply at all if your first conditional verifies the proper environment is there for the second conditional to function. I agree about "premature optimization," but these types of performance hits are harder to spot (your expensive to process method may be perfectly optimized but the question addressed here is whether it needs to be run at all).*

----

Back to HowToProgramInOSX

