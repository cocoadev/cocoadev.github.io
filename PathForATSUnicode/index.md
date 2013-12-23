---
layout: page
title: PathForATSUnicode
---

Since I am using ATSUI for text rendering I figured I would include the proper includes, but what are these? The best one I have been able to find is ATSUnicode.h, but it is in the QD framework under the ApplicationServices framework.

So which file should I include when using ATSUI? And if ATSUnicode.h, what is the path? (I tried adding ApplicationServices framework to my project and used QD/ATSUnicode.h, but it did not work).

----

If it's in ApplicationServices.framework, just     #import <ApplicationServices/ApplicationServices.h> should be ok. See [http://developer.apple.com/documentation/Carbon/Reference/ATSUI_Reference/]

