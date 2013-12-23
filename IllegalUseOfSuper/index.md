---
layout: page
title: IllegalUseOfSuper
---

I get an error: Illegal Use of 'super' when I try to compile this.  What am I forgetting?  BTW: this is in codewarrior.


OK, the problem was an #include instead of a #import elsewhere in the code.  Changing it to an #import fixed the problem.

