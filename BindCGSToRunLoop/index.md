---
layout: page
title: BindCGSToRunLoop
---

Has anyone got any ideas why NSRunLoop or actually CFRunLoop crashes with the following backtrace when running on 10.2? I'm trying to do a cmd-line only, no-GUI application. It's running on a server, which doesn't have a GUI session running. I run it over ssh. On my local 10.3 box the program works just fine.

I couldn't even find any mention of BindCGSToRunLoop one the web with Google ar Altavista.

    
(gdb) backtrace 
#0  0x901365f0 in CFRunLoopAddSource ()
#1  0x96a83690 in BindCGSToRunLoop ()
#2  0x96a48bb4 in FirstEventTime ()
#3  0x90148434 in __CFRunLoopDoObservers ()
#4  0x90180f34 in CFRunLoopRunSpecific ()
#5  0x97e05668 in -[NSRunLoop runMode:beforeDate:] ()
#6  0x97e344f4 in -[NSRunLoop runUntilDate:] ()
#7  0x0000b77c in main ()
#8  0x000073d8 in _start (argc=111212, argv=0x1b2d0, envp=0x1b270) at /SourceCache/Csu/Csu-45/crt.c:267
#9  0x00007258 in start ()


-Petteri

----

The abbreviation CGS seems to refer to the window server. It sounds like something in your app is making it want to start a session with the window server, but there is no window server connection available, and that's making it crash. Could be completely off-base, but that's what it sounds like from the information you've provided.

----

Yes, this is probably what it was. The component responsible for this was CURLHandle.framework. I had to replace it with AGSocket and custom HTTP code to get rid of the window server dependency. WebKit would've been an option, but it had a strange bug that prevented it from working with the particular server I am using with this software. (I didn't get any of my delegate methods called when the server was asking for HTTP Authentication, non-secure pages worked fine. And Safari works fine with that server.)

What I'm doing here is that I'm pulling messages from a FirstClass server and putting them to a PostgreSQL database. Then a PHP script generates a RSS feed from the contents of the database. I'm planning on making this software open source and available to all interested parties (probably about one and a half people :).

-Petteri

----

I'm curious why you've chosen to do this with Cocoa? Wouldn't just doing the whole thing in PHP/Python/Perl/whatever be better?

----

Coz I'm lovin' it! :) There actually was a PHP version, but it I converted it to Cocoa and expanded on that. I'm sick and tired of my badly written PHP scripts which can't be deciphered after about a month of not actively working with the code. A nice class model in Cocoa is so much more readable and modifiable. I feel that I'm more productive and produce better results when using Cocoa. Another reason is that I wanted to do a GUI that lets me browse the results of the actual program's work. The PHP script used to generate the RSS is there just because I was so lazy and I didn't want to code a RSS generator myself (I would have to handle embedding HTML inside the XML and so on).

And hey, if anybody is interested in this software, please say so.

-Petteri

