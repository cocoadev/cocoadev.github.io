---
layout: page
title: XCodeStyleProgressIndicator
---

Does anyone know if there's an implementation of the gray/blue-white progress indicator sitting in the lower right corner of Xcode? You know, the round one visible when compiling.

----
It's most likely a custom control, possibly a subclass of General/NSProgressIndicator, but not necessarily. I'd say a good place to start would be to subclass General/NSProgressIndicator and override it's drawRect method to draw the circular colored style yourself.
----

How about General/ToxicProgressIndicator? http://toxicsoftware.com/blog/toxic_progress_indicator/

----
Wow, that is like... perfect! It is exactly what I was looking for!
----
