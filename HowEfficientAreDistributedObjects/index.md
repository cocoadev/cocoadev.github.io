---
layout: page
title: HowEfficientAreDistributedObjects
---

Yes, how efficient are distributed objects performance wise? Especially compared to Sockets, CORBA, RPC, or Java's RMI? I can imagine that sockets are faster, because they don't have the overhead for managing the underlying objects (but they are definitely not so convenient to use :). So, are there any tests, reviews, stats, opinions, experiences, or anything like that out there?

--ThomasSempf

----

Opinion (you asked for them): DO uses mach ports, and functions essentially the same as (UNIX) sockets.  It just happens to have a nice layer of OOP between you and the raw management of them.  If you use raw sockets, you still have to encode/decode the messaging, and doing it yourself requires you to write a communication protocol over said sockets, which DO takes care of for you.  To make sure DO works at it's best for you be sure to declare and use a formal communication protocol.  If you don't, DO spends extra time checking types and arguments.  In that protocol, use the special modifiers made for DO (oneway, in, out, inout, bycopy, byref).  If you don't declare that it's  asynchronous (oneway), DO assumes (by necessity) that you mean for it to be synchronous and blocks on the response.  If you don't declare a pointer argument to be "in" or "out" DO assumes "inout" which takes much longer to process.  If you don't say bycopy, DO usually does byref...  In every case DO generally takes the least efficient method unless told otherwise, because it most clearly looks like what ObjC does outside of a DO context.  Most of the time your methods are synchronous, with pointers being sent able to pass a value and be modified, and returned pointers could be data being used in another area of the returner (a la NSPrintInfo -dictionary).   To support these semantics across any IPC barrier takes a lot of overhead.  DO makes it very clean and easy to support this if it's what you want, and if you don't makes it fairly simple to optimize.  You could use something else, and you may see some benefit... but I think the return on your work is less than you would hope.  (ie. they are your-time efficient)

----

You are right. With sockets you have to handle all kind of things on you own, so it is very convenient to use object orientated network APIs like DO or RMI. At the moment I am writing a program for my diploma thesis, so I have to argument, why I use that and this technology. For that some statistics or test results would be nice, but I couldn't find anything through googling. Seems nobody did something like that before...

--ThomasSempf

----

Is performance important for what you're doing? If it's not, then "It provides a very easy way to do IPC with a minimum of effort and programmer time" should be more than enough reason to use DO.

----

I did some timing on a PB G3 some time back and found the overhead to be roughly 1ms per call, more or less regardless of the number of bytes sent across (i.e. bycopy).

----

Yes, DO is quite efficient, I have to say. Interesting that during the whole time from NEXTStep to Mac OS X nobody wrote a paper or scientific report about DO and compared it to similar technologies....

--ThomasSempf

