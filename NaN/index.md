---
layout: page
title: NaN
---

Division by zero is an undefined operation, and as such its result is literally Not a Number (NaN). RobRix has taken to signing his e-mails with "R / 0" for just this reason.

----

The one obvious difference between a PPC and x86 is that on x86 division by 0 is a no no, while on a PPC it'll gracefully return 0. That's beauty, baby.

Haha, gracefully be wrong?  I'd rather get the crash. :-)

This is only true for integers. For floats, both platforms will (should?) return NaN.

----

Does anybody know what to do if you get a nan value instead of some very large (or very small) float.  Is there some flag I can set to just get max_int in its positive or negative incarntation instead of NaN?  Help!!!!!!!  Thanks!  (I'm using GCC-3.3)

----

Here's a helpful pdf on the subject. Are you sure you want to just ignore this exception? Page 22 talks about exception handling for NaN exceptions. 

http://developer.apple.com/documentation/Performance/Conceptual/Mac_OSX_Numerics/Mac_OSX_Numerics.pdf

----

Also, at least according to the IEEE spec, NaN != NaN even when they're the same NaN, so it's possible (though a little crufty) to test for it that way.

----

That's exactly how you're expected to test for NaN in some languages. C (and therefore Objective-C) defines the function isnan() (inlined in GCC I believe), which you can also use to check for NaN. -JonathanGrynspan

