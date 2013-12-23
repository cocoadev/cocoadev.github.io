---
layout: page
title: NaNreturned
---

NaNreturned 

I have a method where I want to divide one value by another. Each of these values is the result of the sum of a table column in a tableView. Everything works fine with   add , subtract and multiply operands. When I use the divide operand I get a NaN in the textField; eg

(double)myNewValue
{
    return (double) ([self columnOne] / [self columnTwo]);
}

If I use the any of the other operands it calculates fine. When I use the divide operand as above I get NaN and it won't calculate.

----

First, you probably want that cast inside the brackets, not outside, or you'll lose precision. Second, is     [self columnTwo] zero perchance?

*That would probably give it infinity (and yes, there is a float constant for infinity, as well as NaN, negative infinity, zero, and negative zero). Assuming that     [self columnOne] and     [self columnTwo] are floats or doubles, they most both be zero to result in NaN. The above poster is right about the casts though...you'll gain more precision if you cast before, not after. --JediKnil*

It might get displayed as     NaN, though.

Divide by zero isn't infinity, because there's both positive and negative infinity, and neither one fits. NaN is basically what corresponds to "undefined" in mathematical terminology, so that's what you get if you divide by zero, take the square root of -1, etc. You'll get +/-infinity if you divide a really big number by a very small non-zero number, take the log of 0, and things like that.


----
Thanks JediKnil! 
The two column values start out at zero until a row is added to the table column which gives me the "NaN" return. As I add data into the tableView  rows the  column value will either be a 1 or 0. The column values are totaled and I'm trying to get a ratio of the two total column values. As long as the denominator value remains 0 it will give me NaN. but when the denominator changes to greater than 0 is still gives me a NaN return. It works fine with the other types of operands. Also when you say "cast inside brackets" do you mean e.g.
    
(double)myNewValue
{
    return (double) ([self columnOne / self columnTwo]);
}

I tried that it won't compile.

*No, he meant this: --JediKnil*
    
-(double)myNewValue
{
    return ((double) [self columnOne] / (double) [self columnTwo]);
}


NSLog the results of the two method calls, log the result of the divide, and post the logs.

**In fact, you only need one of those casts (the other will then be implicit):     (double) [self columnOne] / [self columnTwo] or     [self columnOne] / (double) [self columnTwo].**

----

Remember that an integer divide by zero on x86 is really BAD!!! (I know this is not the case for floating point numbers, but I thought I would place a reminder here).

http://developer.apple.com/documentation/MacOSX/Conceptual/universal_binary/universal_binary_diffs/chapter_3_section_8.html

--zootbobbalu

