---
layout: page
title: RoundingFloatingPointValues
---

I'm using Xcode 1.1, and I am in vain trying to round off a floating point value to, for example, two decimals. i've found a method that will round it off to zero decimals, but that's not what I want. Does anybody know how to do this?

----

Crude hack (that may or may not work)
[[NSString stringWithFormat:@"%.2f", theFloat] floatValue];

----

Well, if we're talking crude hacks, wouldn't      round(theFloat*100) / 100; be a simpler crude hack?  -- Bo

----

How is that a crude hack, Bo? Looks pretty straightforward to me.

Anyway, if your goal is to do it all in cocoa then you can use the NSDecimalNumber class.  Bo's way is more straightforward though.

NSDecimalNumberBehaviors mentions a little about the rounding behavior included in that class

----

It should be noted that there are many two-digit decimal values, such as 0.1, which cannot be exactly represented by a binary number because they're binary, not decimal. If you need *exact* representation out to two digits right of the decimal point, then you should use an integer type and multiply/divide by 100 (or a different number as appropriate to change the number of digits) when displaying data to the user or taking input from the user.

----

If you simply need to round a float either up or down (if you know on which side of .5 it lies) you can do ...

float roundedUp = ceilf(myFloat)
float roundedDown = (int)myFloat

If you just want to round it use the latter.

JKP

----

Since it hasn't come up yet..

The usual thing to do is to not round a float internally, but to only *display* it rounded to two decimal places.  printf, NSString, and NSFormatter have ways to do this.

----

I've been using this macro for quite a while:

<code>
#define round(x) ((x)>=0?(int)((x)+0.5):(int)((x)-0.5))
</code>

----

Or, you could just     #include <math.h> and use the built-in     round() function (or another built-in function if you want different rounding behavior), avoiding both macro evilness and the painful speed penalty of the float-int conversions that the above macro will cause.

*Uh, isn't that like extreme premature optimization?*

No, using the provided library functions instead of a broken macro is just good programming sense.

*Why are macros "broken?"*

Macros in general are not broken. But this one is for at least four reasons.


* It evaluates its argument twice. So if you have a parameter with side effects, the results will be surprising. For example:     float x = 1.6; float y = round(x++); After this is evaluated, x will contain 3.6, and y will contain 3.0, instead of the expected result of 2.6 and 2.0.
* Arguments larger than     INT_MAX will produce undefined results, whereas the built-in function handles this case correctly. This is particularly important when using     doubles, as they have enough range and precision to be able to hold a number much larger than     INT_MAX while still having significant figures to the right of the decimal point. Even when such significant figures do not exist, the built-in     round will work properly and this macro will not.
* It is unnecessarily slow. Conditional branches are slow, and this macro uses one. Float-int conversions are extremely expensive, and this macro will perform two if the result is placed into a     float or     double. The build-in     round function, assuming it's implemented in a smart way, will use no conditional branches nor float-int conversions.
* The required functionality already exists in a system-provided library. If the function which is already present works correctly, provides the needed functionality, is not buggy, and is sufficiently fast, then writing a macro which does the exact same thing is pointless and broken. Existing functionality should always be used when possible.


*Unfortunately, on OS X 10.4, running <code>man math</code> only gives a 'this page is currently being updated' message, so we have no way to know what the math library does or does not provide.*

Fortunately for us, the man pages for the individual functions such as     man round are intact. And if you don't know the name of the function,     math.h has a convenient list of every single provided math function.

