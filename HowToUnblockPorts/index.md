---
layout: page
title: HowToUnblockPorts
---

I have this recurring problem when developing client/server apps that when the server quits the listening port becomes blocked.  Attempting to release the listening socket on 'applicationWillTerminate' doesnt seem to remedy this.  If I quit the app and relaunch it right away its listening port is blocked and it cannot start listening.  Obviously the client then cannot connect to it until I quit the server and wait for some timeout.  Is there a way to make sure the port gets freed when the applicaton quits or else to reset the port before attempting to listen upon launch?

EcumeDesJours

----

When the process owning the port terminates, the OS will make it free for re-use.  It sounds like your program hasn't finished terminating before you are trying to restart it.  Are you explicitly joining on the old process before starting the new one?  That should be sufficient.

--JeffDisher

----

See, you'd think that, but .... what really happens when you terminate is that it puts all your used sockets into a wait state until the TCP connection closes down properly.  So, if you start up quickly enough it may have not had time to close all them properly, and they'll still look busy.  This is usually only a problem in server apps because all connections are to the same local port number, so all of them have to shut down before you can reuse the port number.  The solution is to set the SO_REUSEADDR option on the listening socket before binding it to the port; however, depending on the framework you're using, that may be tricky to implement. -- Bo

PS I found the BSD sockets faq invaluable for this type of stuff (http://www.manualy.sk/sock-faq/unix-socket-faq.html ).  Numbers 4.1, 4.2, 4.5 and 2.7 explain this 'interesting' quirk.

----

I am using AsyncSocket, which is CFSocket based.  I've bothered Dustin enough lately but maybe he'll read this and be inspired to implent this in his framework...  :-)

btw: why would you ever NOT want to have your sockets available again immediately?

EcumeDesJours

----

The FAQ really explains it better than I could.  To sum it up, networking is tricky, and the sockets API is trying to protect you from a very subtle, very rare problem that can occur if a port number is made immediately available upon socket close.  The thing is, it's so ridiculously rare that most people would rather just ignore it, hence the SO_REUSEADDR option. -- Bo

----

Thanks for the quick reply, Bo.  I looked through all that and more online and really understand it in theory - I just don't know how to implement it in my framework.  Obviously I need to set it with setsockopt() but in order to do that I need the socket descriptor and with the AsyncSocket api it is not clear how to get this...

----

In CFSocket, you can use the return value from CFSocketGetNative as the socket descriptor for any of the BSD sockets calls, like setsockopt, so it'd look a little like this:
    
int reuseOn = 1;
setsockopt(CFSocketGetNative(myCFSocket), SOL_SOCKET, SO_REUSEADDR, &reuseOn, sizeof(int));

I'm not familiar enough with AsyncSocket to tell you where that's gotta go, but it's gotta be after the CFSocket is created but before it's bound (i.e either connecting or listening).   -- Bo

----

Thanks Bo - that totally worked.  I added this info to the AsyncSocket entry on CocoaDev.

EcumeDesJours

