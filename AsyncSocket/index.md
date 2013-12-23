---
layout: page
title: AsyncSocket
---

AsyncSocket is a class that wraps CFSocket and CFStream. You can download it from:

*<strike>http://code.google.com/p/cocoaasyncsocket/</strike>
*https://github.com/robbiehanson/CocoaAsyncSocket
*Docs at http://files.me.com/d_j_v/96o05n


What I look for in a socket is fire-and-forget functionality and asynchronous operation. That is, I don't expect the socket to block under any circumstances, and I expect to tell it to read x number of bytes and have it tell me when it finishes. I checked out DizSocket and NetSocket, but they weren't up to par, so I wrote AsyncSocket. Here are the key features:


*Queued **non-blocking reads and writes**, with timeouts. You tell it what to read or write and get out of the way. It'll call you when it is done.
*Automatic **socket acceptance.** If you tell it to accept connections, it will call you with new instances of itself for each connection. You can, of course, disconnect them immediately.
***Delegate support.** Errors, connections, accepts, read completions, write completions, and disconnections all result in a call to your delegate method.
***Run-loop based**, not thread based. Although you can use it on main or worker threads, you don't have to. It calls the delegate methods asynchronously using NSRunLoop. The delegate methods include a socket parameter, allowing you to distinguish between many instances.
***Self-contained** in one class. You don't need to muck around with streams or sockets. The class handles all of that.
*Support for TCP streams over IPv4 and IPv6. I haven't implemented UDP support, and don't plan to. UDP is just too different. But if you want a UDP version of this class, e-mail me at d.j.v.@mac.com and I'll take it into consideration.


Comparing AsyncSocket to other libraries, DizSocket does not accept connections and SmallSockets is more difficult to use. I dare say that AsyncSocket is the best socket library out there short of OmniNetworking.

----

Version 3.13 (http://files.me.com/d_j_v/1wzc1a) can be used with Mac OS X 10.3.

Version 4.3 (http://files.me.com/d_j_v/cid2rx) requires Mac OS X 10.4, and has the following changes from 4.21:

*Eliminated empty data returns from -readDataWithTag:.
*Added per-socket user data.
*Fixed null-padding at end of recovered data.
*Better DNS-related NSError messages.


Later versions are maintained by Duesty Designs.


----

Just to say that my experience with AsyncSocket has been flawless so far, and better than any of the alternatives I tried.

----

Me too, about a year ago.  And since then, he's updated it, so it's still alive and well.  Good to know.  -- Larry Gerndt (lgerndt@mac.com)

----

I try to use AsyncSocket for sending and receiving continuous data streams (audio).
When using AsyncSocket from within the main thread, all works fine so far but obviously when the user opens a menu or uses a GUI control, the AsyncSockets disrupt the send/receive streams. 
So I tried to create and use the AsyncSockets from within a worker thread with its own NSRunloop. I've implemented     -onSocket:wantsRunLoopForNewSocket: and return the current runloop of the worker thread. Accepting/connecting works fine, but the sockets created by the accepting/connecting process don't call any delegate functions after that.
As said before, the same code works fine when used from within the main thread.
Anyone knows what I'm doing wrong? Is there any sample code for using AsyncSocket from within a worker thread available?
Thanks!
--Eberhard Rensch (mail@pleasantsoftware.com)

----
I finally found the solution myself: The problem was, that I've called     NSRunLoop currentRunLoop] runUntilDate:[NSDate dateWithTimeIntervalSinceNow:.1 to sleep inside the worker thread. 
After examining the AsynchSocket sources for a while I figured out, that all Runloop specific stuff in there is done by CoreFoundation functions (CFRunLoopAddSource and so on). So I've replaced the above NSRunLoop call with     CFRunLoopRunInMode(kCFRunLoopDefaultMode, .1, false);. It seems to work now.

----
It appears that AsyncSocket is being maintained by Deusty Designs (the company that makes the Mojo music sharing software):
http://deusty.blogspot.com/search/label/AsyncSocket

----
I am the original author of AsyncSocket. I have no problem with someone else taking over. It's in the public domain, after all. Less work for me! :-)

----

MarketcircleInc uses AsyncSocket as the foundation of its new client-server protocols. Thank you, DustinVoss.

