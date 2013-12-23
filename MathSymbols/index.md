---
layout: page
title: MathSymbols
---

Hi.  I'm making a program that allows people to practice the chemistry symbols and valences.  To make a program like this though I have to be able to write things like NH4 +1 or something like that except the 4 should be smaller and at the bottom corner of H and +1 should act like an exponent.  Does anyone know how to format a text field to act like this?

Gabe

----

You mean superscript and subscript?

Normally you would use an NSAttributedString instead of an NSString and then set the styles, like     NSSuperscriptAttributeName �� though I can't find the attribute name for subscript. But do you want to use this for interactive editing?

--AllanOdgaard

----

You may have issues getting the +1 to display directly above the 4. Depending on what you're looking for, you might be better off using LaTeX or something.

