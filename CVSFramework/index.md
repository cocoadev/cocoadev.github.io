---
layout: page
title: CVSFramework
---

Is there a framework available that can be used to call CVS commands from within Cocoa?

thanks,

KoenvanderDrift

----
You will need to use NSTask to invoke a CVS command.  Or else get the CVS source and see if you can extract the useful logic from the command.  If you can use subversion, it has an easy to use client API.
----
NSTask it will be then, not too difficult :)
Thanks.

