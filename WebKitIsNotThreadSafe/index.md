---
layout: page
title: WebKitIsNotThreadSafe
---

General/WebKit is not thread-safe. At least, as far as anyone knows.

The following code, when launched from for (i=0; i<5; i++) in separated threads indexed with i, fails with SIGBUS.
:
    
	General/WebView *view = General/[[WebView alloc] init];
	[view stringByEvaluatingJavaScriptFromString:@"document.location.href='http://images.google.com/images?hl=en&lr=&ie=UTF-8&q=6&btnG=Search'"];
	[view setResourceLoadDelegate:self];
	unsigned j=1000000;
	while ((j--))
	{
		General/[[NSRunLoop currentRunLoop] runUntilDate:General/[NSCalendarDate distantPast]];
	}
	[view release];



----

If something is thread-safe, then you can access it from multiple threads without worrying about simultaneous access.

If something is not thread-safe, then you can't access it from multiple threads without explicit locking. In a context like Cocoa, where there is so much going on behind the scenes, it often means that you can't access it from *any* thread besides the main thread, because it's impossible to lock everything that a complex object accesses when it doesn't even tell you what it accesses.

Unless General/WebView is explicitly documented somewhere as being thread-safe, then this code is wrong and dangerous. Without that explicit documentation, you must assume that it's only safe to access General/WebView<nowiki/>s from the main thread.

----

The code cannot be cleaned up unless the threads are eliminated, and the only thing to understand about using General/WebKit with threads is, as far as I know, "you can't."

----

Also, be aware that "thread-safe" is often (or at least, used to be) used in Apple's documentation to mean "can be accessed from a worker thread, not just the main thread". As far as I know, no Cocoa classes are actually thread-safe by the above definition except the General/NSLock family. The reason was simple: locks are always necessary to achieve thread-safety, but cost significantly when thread-safety is not required; hence, locking must be done manually.

*
I think this is not correct.  See http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/articles/General/CocoaSafety.html for details.

Example quote: "The classes and functions in the following table are generally considered to be thread-safe. You can use them from multiple threads without first acquiring a lock."
*

Ah, silly me. Immutable things generally don't need locks, do they :) As for the others, some make immediate sense, and as for General/NSLog -- hurrah! Non-thread-safe error messages are a pain.
