---
layout: page
title: RetainCycles
---



RetainCycles can occur when you are attempting to make sure your code employs proper ThreadSafety. Basically, what happens is that the first object retains the second object, and the second object retains the first object. So, neither will ever be released, which causes MemoryLeaks.

In order to avoid RetainCycles, you can AutoRelease objects, or you can employ the concept of WeakReferences.

In case you're not sure just what exactly retaining is, we have information about RetainingAndReleasing and the RetainCount for you. If you're wondering exactly what a retain cycle is, see DirectedCycle.

