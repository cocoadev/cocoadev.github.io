---
layout: page
title: FixedPoint
---

FixedPoint is a way of representing non-integer numbers without some of the pitfalls of FloatingPoint math. Advantages are speed, particularly on computers without FloatingPoint hardware, and exactness; FixedPoint does not suffer from the various small calculation errors that tend to exist in FloatingPoint code.

Most languages and computers do not have native support for FixedPoint arithmetic. Instead, it is handled by storing the FixedPoint numbers in normal integers, and manually handling the transformations needed to perform the arithmetic. The basic idea of FixedPoint is to multiply the number by a certain factor, such as 256, before storing it in the integer. This gives you some space to use for fractional values, at the expense of range.

For a more detailed discussion of FixedPoint, see http://en.wikipedia.org/wiki/Fixed-point_arithmetic

