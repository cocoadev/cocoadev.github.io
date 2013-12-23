---
layout: page
title: FourCharCode
---

A datatype used to hold four bytes like a creator code.

They are used like:     FourCharCode myApp = 'myAp'

Cocoa APIs don't use four-char codes like the Carbon APIs do. When you want to supply a four-char code to a Cocoa method, you wrap it in an NSString like so:     [foo methodWithFourCharCode: @"'MyAp'"]

----
I was under the impression that this was not allowed by the C standard, because e.g. of endian-ness.

So one would instead do:
    
#define MAKE_ID(a, b, c, d) ((a << 24) |�(b << 16) |�(c << 8) |�d)

MAKE_ID('m', 'y', 'A', 'p');

But I cannot find a function/macro to do that in Apple's headers.

----

    
int abc = 'abc ';
char *fourCharacterCode = (char *)&abc;
fourCharacterCode[3] = 0;
NSLog(@"fourCharacterCode: %s", fourCharacterCode);


basically you can assign a four character code by using single quotes. I'm not sure how long GCC will support this though.

----

A four-char code is just an int. The character array in the example above is just to print out the code in ASCII instead of numeric form. Basically, GCC supports putting 'MyAp' in an integer like it supports putting 53 in an integer.

----

And how much it will screw up things if e.g. we change to x86 which is little endian -- though there are probably also many other endian issues that many coders have ignored -- e.g. mapping 'struct' to memory read from a file to make it easer to parse it etc.

Btw: you can print the codes like this:     printf("%.4s\n", &fourCharacterCode). And btw: you do not want to declare it 'int', since that is signed. <- * don't you mean     printf(%.4s\n", fourCharacterCode)* and for this example it doesn't matter if it's a signed int or an unsigned int, but your recommendation is a good practice. I was just trying to show that the assignment worked. 

**No, I do not want to use the int as char*, I want to use address of int as char***

Another problem might be changing e.g. to IBM's compiler, if 4 character constants is a GCC extension (which I think it is).
----

I am too lazy at the moment to track this down in a standards document, but I can verify that four char codes do compile without warning under both the Sun Microsystems compiler (non-gcc) and Microsoft Visual C++. I assume the IBM compiler would follow suit. If this is an "*extension*", it seems to be a popular one.

The *endian issues* are easily taken care of, always have been, always will be. Even the MSVC headers are full of endian *adjustments*, out of necessity.

BTW, IMO, I don't think we will be changing to x86 anytime in the near future, It might be the other way around (see Xbox)... ;-)

----

The easiest way to convert a FourCharCode to a NSString is to use the function NSFileTypeForHFSTypeCode

