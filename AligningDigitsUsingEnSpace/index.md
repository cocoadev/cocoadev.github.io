---
layout: page
title: AligningDigitsUsingEnSpace
---

I want to prefix the lines in an outline view with an integer value which consist of a variable number of digits.

To make it look good I want the values to be right aligned, e.g.:
    
   1: foo
  23: bar
 234: fud


I think there is some typographic "rule" that says that digits should be the same width, even for a variable spaced font, and surely the digits do align, but the space is not the width of the digits � so my question is, is there some unicode space which should be the same width of a digit?

I figured en-space would be a candidate, and surely this aligns quite nicely:

nnn1: foo

nn23: bar

n234: fud

But the en-space in the font I am using is off by one pixel or so � so maybe my assertion is wrong?!?

P.S. I do know that I could change the strings to NSAttributedStrings and insert a right-alignment tab-stop and use tabs in the strings, but that's a lot of work I'd prefer to avoid :)

----

The solution was Figure Space (0x2007): *In fonts with monospaced digits, equal to the width of one digit*

