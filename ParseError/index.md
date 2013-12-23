---
layout: page
title: ParseError
---

Original discussion pre-pended to ParseErrorAtCertainNameNeedHelp, which contains other examples of how to fix a ParseError

----

Hopefully the following list will expand into a bestiary of typical causes for parse errors, concisely described...

The ParseError can happen during compilation for a number of common reasons:



*Many (if not most) of your typical garden-variety C syntax errors.

* **The compiler absolutely must be informed about every type before you use it**. Many parse errors occur simply because you have failed to declare a variable properly, particularly instance variables. This can happen, for example, if you have not imported a required header.

* A ParseError will also appear, for example, if you fail to use **@** to tag a constant NSString instance where one is expected.

