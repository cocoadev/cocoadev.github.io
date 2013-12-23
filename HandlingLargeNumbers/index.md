---
layout: page
title: HandlingLargeNumbers
---

see also NumericalComputation

Ok this isn't Cocoa/Obj-C related, but I figured I'd ask. What's the best way to go about handling huge numbers? I need to handle extremely large numbers in decimal format that are similar in size to this (example):

*30025024017030974982992992990750720750004017000004008050480480000000*

And it appears as if unsigned long long ints max out at 18446744073709551615. I saw that there is a BigInt module or whatever for Perl but I haven't seen anything like it for C although I'm sure there's probably a class out there somewhere... Anyone have any suggestions?

----

NSDecimalNumber can handle large numbers, up to a certain limit. It's a bit unwieldy and I'm sure it's rather slow, but it's part of Cocoa.

If you don't mind external dependencies, GnuMP is an excellent large-number library that's limited only by available memory.

----

I've tested Victor Shoup's NTL (Number Theory Library) - just barely so far.
It has (at least) plenty of support for big numbers, integer and decimal types and was a good place for me to begin.
My impression is that the learning curve may be a little less steep than for GnuMP, with the following caveat.

It uses C++ iostream classes to return values from functions so you need to be comfortable with that before you dump the results into a Cocoa window.

http://www.shoup.net

Furthermore:

MXKit is a set of Cocoa classes for doing arbitrary precision things (and much more) -- http://synthemesc.com
Released under LGPL.

The download includes a PB project to build the framework. GnuMP is included.

Of course, the Cocoa code is another layer that you kinda have to test as well. But the package includes some interesting classes, and
goes well beyond the usual practical arb-precision concerns.

----

Great thanks for the info. NSDecimalNumber looks like it might be enough but I need to check out its max value first before I do anything.

Hmm I just pasted that example number into Calculator, it formatted it into scientific notation, but the paper tape showed it as full decimal format. I wonder if they use NSDecimalNumber.

----

BTW the bc unix utility works with large numbers like this. I could maybe write a wrapper for it or something...

----
The easiest and fastest to implement solution I saw was the bc command, so I created a wrapper for it. See BCWrapperSource

Also see ConvertStringIntoExpression
----

----

The docs for NSDecimalNumber clearly state that is based on the NSDecimal structure; the NSDecimal structure is only good for 38 digits.

----

As someone already mentioned, Take a look at the GNU Multiple Precision Arithmetic Library.

If you are just dealing with integers, there is also multiprecision integer arithmetics in OpenSSL already availble on your box.

See **<openssl/bn.h>** and **man 3 bn**

