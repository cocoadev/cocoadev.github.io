---
layout: page
title: RefactoringImprovingTheDesignOfExistingCode
---

Book by MartinFowler &al. about refactoring.

WhatIsRefactoring ?

One of the important books whose concepts should be known to all ObjectOrientedProgrammers.

http://www.amazon.com/exec/obidos/tg/detail/-/0201485672

This book is really neat (although very much targeted at the Java audience).  It talks about refactoring in general, what it is (moving around code to make its design better, but not adding any new features), and why you'd want to do it (cleaning up after a feature implementation phase), and then it has a catalog of different refactoring techniques, like if you have two methods in peer classes that do the same thing, then  you can push that method up into the parent class and the subclasses then will share the same implementation.  You've now reduced the amount of code you have to maintain/ship, but have the same functionality.

