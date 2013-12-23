---
layout: page
title: HowDoYouUseNSUncaughtExceptionHandler
---

Anyone know how the hell to use NSUncaughtExceptionHandler?? The two (highly rated) Cocoa books I have have zero mention of exceptions - it's as if the exceptions don't even exists! And apple's documentation is pathetic on the subject.

Are there ANY *clear* examples ANYWHERE????

----

There's really not much to explain:  There's the function     NSGetUncaughtExceptionHandler(), which returns a pointer to the current uncaught exception handling function, and there's     NSSetUncaughtExceptionHandler(NSUncaughtExceptionHandler*), which accepts a pointer to an uncaught exception handler function.  If you pass in a pointer to a function you wrote, then your function will get called when an exception doesn't get caught.  -- Bo

PS I haven't used these functions, but I remember that someone complained about these functions only working in secondary threads; in the main thread, the application installs it's own exception handler which keeps it from ever falling through to the uncaught exception handler.  Caveat emptor.

----

That's probably why I'm getting no result out of it.... Thanks for the heads up.

