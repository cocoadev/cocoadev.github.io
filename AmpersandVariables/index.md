---
layout: page
title: AmpersandVariables
---

This is probably a very basic thing, but I have seen in code used (see "- (unsigned)lineCount" function on NSStringCategory page) that uses variables with an "&" pre-pended to them.  I could only guess that this incremented them somehow.  Can someone tell me how they work, or better yet, point me to a place on this site that I have missed in my search?

----

This means "address of" the variable.  It lets you do many things, and in this case it looks like it is being used to that the method can change the value of the variable being passed, which is common in methods that need to give you more than one piece of information.  Maybe someone could point this guy to a nice site on pointers? - JamesCallender

----

http://www.murrayc.com/learning/cpointers.shtml#PointersAreAddresses

----

This issue will probably be discussed in O'Reilly's new "C is for Cocoa" series:

http://www.macdevcenter.com/pub/a/mac/2003/07/22/cocoa_series.html

