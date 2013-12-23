---
layout: page
title: DyLan
---



Dylan

A language project at Apple (killed a few years ago), Dylan stands for "Dynamic Language". 

Some of the characteristics of the language are:
* MultipleDispatch (function resolution at runtime depends on *all* params)
* Full dynamic object typing with full introspection.
* Ability to add instance variables and methods to a class, while the program is running.
* The compiler has enough compile time type info (if the programmer chooses to inform it) that truly fast code can be generated. That is, in order to avoid the overhead of dynamic dispatch (not real efficient when doing arithmetic for example), the programmer can specify more type info than just "its an object", this was supposed to allow near "C" runtime speed as a tradeoff to runtime dynamism; the programmer gets to make the tradeoff.

It was originally a Lisp variant, but in an effort to be relevant to non-Lisp programmers, Apple changed the syntax. Didn't help enough to bring loads of new programmers on board. Anyone thinking now about the Objective C "modern syntax" debacle Apple went through? Here's what the syntax looks like now as seen in a sample dylan implementation:

Example from www.dylanpro.com:
http://www.dylanpro.com/picts/dylanProjectBrowser.gif

Apple Cambridge developed a complete Dylan IDE ("AppleDylan") in MCL. AppleDylan had  some very nice features: incremental compilation, a source-code "repository" instead of files, ubiquitous module system, automatic C interfaces, integrated debugger with dynamic code updating, and a very nice browser (shown above).

Apple's storied history with dynamic languages includes MCL, SmallTalk80, SK8, ScriptX, NewtonScript, and (now) ObjectiveC.

Dylan still has its fans. There are various implementations available. Start searching at these links:

* http://monday.sourceforge.net/wiki/
* http://www.gwydiondylan.org - They have Mac implementations
* http://www.functionalobjects.com/resources/index.phtml
* http://www.dylanworld.com/ - No permission to access "/"?
* http://www.dylanpro.com/DylanExchange.html
* http://www.cise.ufl.edu/~jnw/Marlais/

-- TimGogolin
-- Dustin Voss added links

