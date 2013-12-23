---
layout: page
title: AspectCocoaBugs
---

**AspectCocoaBugs** - 

Applying before advice to an alloc method, or after advice to a dealloc method will both lead to crashes because AspectCocoa attempts to get information from a "target" wich is nill... this is a known bug. Any ideas as to what AspectCocoa could do to fix this, other than preventing users from advising alloc or dealloc methods?  this is not a problem for init methods.  The solution to this bug is not a programming topic, it is a matter of design theory... do you allow your users a mechanism that will crash when they use it incorrectly, or do you take it away from them removing tha ability to use it at all.

There are a zillion issues with advising methods on NSObject... but we should be able to advise any NSObject methods which is declared in a subclass.

There is a conflict between ZeroLink and the Objective-C runtime function: objc_getClassList().  Thus, the default PointCut will return and incomplete list of Classes and Methods if ZeroLink is enabled.

