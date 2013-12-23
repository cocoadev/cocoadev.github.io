---
layout: page
title: SocketClasses
---

If you know of any other classes, stick 'em on here. Feel free to comment


***AGSocket** - http://www.sourceforge.net/projects/agkit/
***AsyncSocket**
***EDCommon** - http://www.mulle-kybernetik.com/
***NetSocket**
***OmniNetworking**
***SmallSockets**
***TCPServer** - http://developer.apple.com/samplecode/CocoaHTTPServer/ - Apple's handy nonblocking server class.
***FastSocket** - https://github.com/dreese/fast-socket

 http://www.le-meilleur-forfait.com http://www.releve-identite-operateur.fr https://twitter.com/Meilleur http://www.obtenir-rio.info http://www.portabilite.info

See also CFSocket, NSStream

----
**General References**

Beej's guide is a good basic intro: http://www.ecst.csuchico.edu/~beej/guide/net/  (the Stevens books, while excellent, gets into way too much detail for just basic sockets setup).  ++MarkDalrymple

The Stevens books:
UNIX Network Programming  [http://www.forfait-mobile.info Forfait Mobile]
http://www.amazon.com/exec/obidos/ASIN/013490012X/

Advanced Programming in the UNIX Environment 
http://www.amazon.com/exec/obidos/tg/detail/-/0201563177/

There's a great article on this at StepWise. Here's the link: http://www.stepwise.com/Articles/Technical/2003-04-22.01.html --KevinPerry

----

Anyone care to offer up some differences between the above classes? Maybe some of you have some experience with using them for different purposes.

--ZacWhite

----I don't think I'm eligible. -- DustinVoss (author of AsyncSocket)

---- I've used both AsyncSocket and NetSocket.  AsyncSocket is a nice little class, great for small network tools, but (in my experience) completely unsuitable for any sort of significant application: there's evidently no way to set a read timeout which doesn't completely close the socket.  If you're dealing with, for instance, a telnet stream -- where you may need to process IAC sequences -- but the majority of things you'll be doing are delimited on the CRLF boundaries, there's no way to have a behavior of 'read until you encounter one of these characters OR it times out.'  (Or if there is, it's neither obvious nor documented.)  NetSocket is more basic (particularly, it lacks AsyncSocket's really lovely helpers for reading until you reach certain data characters) but is much more flexible in terms of just general 'I need data from a socket and it could be time to read in any number of situations.'  Dunno if that helps anyone. :) --RachelBlackman

----

It is not difficult to write your own socket class.  The BSD socket API is pretty simple.

The information ahead assumes you want stream sockets (TCP) and not others (UDP, etc)

Some Unix manpages of interest:

Socket API: socket(2), connect(2),  for servers: listen(2), bind(2), accept(2)

File descriptor API: read(2), write(2)

More specific versions for sockets: send(2), recv(2)

DNS on newer systems (if you want to support IPV6 use this): getaddrinfo(3)

DNS on older systems: gethostbyname(3)

Cocoa also has NSHost for DNS lookups

Notes about blocking: by default, sockets are blocking, which means upon reading or writing your program grinds to a halt until it finishes the operation or gets an error.  If you're writing a GUI application obviously you don't want this.  Using a separate thread for each socket is also overkill and will likely (1) introduce locking bugs and (2) overwork your scheduler.  What you'll probably want to do is put your socket in non-blocking mode (with the C call: fcntl(socketFD, F_SETFL, O_NONBLOCK)).  Then, read/write/send/recv will not block.  They'll instead return -1 (with errno == EAGAIN) if it can't send right away.  What you want to do is have your code buffer data that gets written and read.  Have your socket class's -write:(const void*)length:(int) method simply copy the bytes to a buffer and worry about it later.  Then, in an NSRunLoop, have it actually try the writes using the write() system call.  Do the same thing for read(), storing the data in a separate buffer, and develop some way to notify your program that the class has received data (creating a delegate with a method like -gotBytes:(void*)length:(int) is a good way to do this.)  So, in other words, your socket has an event loop where it tries to flush the data in the out buffer and read new bytes, upon which it will notify a class delegate of the data that comes in.

If you want to check to see if there is data in the socket or if it is ready to write without actually calling the read()/write() system calls, there are a few calls to do this.  poll() is the more modern one, it is present on Solaris, Linux, *BSD, however Mac OS versions older than 10.3 do not have it.  select() is the more "traditional" way to do it, it's a BSD API from the 1980s, and is present in all versions of Mac OS X, but it's a bit uglier to use, and can only handle sizeof(int)*8 file descriptors.

Always remember to check the return value of read() and write().  If they are positive, they return the number of bytes that were read or written, which is not necessarily the same as what you specified.  If they are 0, it means the other end closed the connection.  If they are -1 you should check the global variable errno.  If it is EAGAIN, try again later, otherwise, some sort of error occurred.  (Use strerror(errno) to get an error message, or [NSString stringWithCString:strerr(errno)] to get an NSString.)

-- Anonymous Unix programmer

----
Removed discussion about NSSocketPort which is only used for distributed objects
----

----
AsyncSocket is maintained regularly by Deusty and the Mac development community.  It even works on the iPhone:
http://code.google.com/p/cocoaasyncsocket/

There is also a vastly improved version of Apple's TCP server here:
http://code.google.com/p/cocoahttpserver/

----
The AsyncSocket project mentioned above now contains both a TCP and a UDP class.

----

Pure UNIX socket sample code (which teaches the basics of socket programming and may also be helpful for writing your own socket class): 

socket_test, http://my-sample-code.googlecode.com/svn/trunk/socket/

----

Just added FastSocket to the list. It's a simple, synchronous class designed for speed. --DanReese (author)

