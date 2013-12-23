---
layout: page
title: ThreadedWebView
---

**Q.:** How do I make use of webView in other thread?
I decided to make a `spider' object, running in separate thread, wich will feed webView with desired General/URLs, but this idea failed -- webView seems to be not thread safe at all. Using General/NSTextField seems to work.

----

**A.:** It's very easy to have something run primarily in a separate thread, then message back to the main thread for operations like this, using     performSelectorOnMainThread:.... Since General/NSTextField is not listed as being thread-safe, most likely the version it works for you is that you've been getting lucky. Thread safety is often like that; 99.9% of the time it works fine, and 0.1% of the time it fails in a bizarre manner. I suggest reading http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/Concepts/safety.html to find out exactly what is and is not thread safe in Cocoa.

----

Thank you for     performSelectorOnMainThread:�. I clean up this thread, so it looks like part of FAQ.
