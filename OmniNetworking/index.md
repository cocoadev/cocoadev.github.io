---
layout: page
title: OmniNetworking
---



OmniNetworking provides a simple and extensible Objective-C wrapper to a multitude of complex networking constructs. Communication over several internet standard protocols is supported, including TCP, UDP and Multicast. Writing a simple FTP client, or custom TCP/IP-based server program becomes a trivial task using OmniNetworking.

----

Crash course in OmniNetworking -- Please add/correct as necessary.

**
Getting OmniNetworking
**

OmniNetworking is one of the OmniFrameworks.  Before you can use it, you must first download and compile it:

http://www.omnigroup.com/developer/sourcecode/

Note that you may need to download multiple frameworks -- OmniNetworking has some dependencies on a couple of their other frameworks: You'll need OmniBase and OmniFoundation.

You have two options when using the OmniFrameworks:


* Compile them as seperate entities into ~/Library/Frameworks:
To compile some of the frameworks, you may need to configure ProjectBuilder's preferences for a "separate location for build products" (perhaps a folder in your home directory), and configure the framework project's target to match whichever location you use.

* You can also put the frameworks into your own project. This has the disadvantage of making the resulting Application much bigger, but on the upside you don't have to count on the user to have the frameworks installed. (That's the way OmniWeb uses the OmniFrameworks.)

After you get the frameworks successfully compiled, you should hang on to the source.  It is a useful reference.

**
Creating a socket
**

Creating a standard TCP/IP socket is as simple as this:

    
ONTCPSocket *mySocket = [ONTCPSocket tcpSocket];


Note that by default, the new socket (like most OmniNetworking objects) will be autoreleased, so be sure to send -retain to it if you want it to stick around (say, if it's a DataMember of a class).

ONTCPSocket is a subclass of ONInternetSocket, which in turn is a subclass of ONSocket.  This simple hierarchy allows a basic set of socket control methods to be inherited by sockets of varying types.

**
Connecting to a remote host
**

You'll need to create an ONHost object for the host you intend to connect to.

    
ONHost *host = [ONHost hostForHostname:@"www.whatever.com"];


This will perform whatever name server lookups are necessary to resolve the host name.  There are other ways to create an ONHost as well; refer to the header file.

Next, to actually connect to the host:

    
int portNum = 25;
[mySocket connectToHost:host port:portNum];


This connects to the host on whatever port you specify.  For example, 25 is usually the sendmail port.

If network conditions are favorable, you should now be connected.

**
Reading from the socket
**

Most sendmail servers send some sort of "welcome" message whenever you connect.  Let's try to read that.

Sending -readString to the socket will read whatever bytes are currently available for reading.  If no bytes are available, it will block the thread until some bytes are available.  (Note that sockets can also be made non-blocking, but that is not covered here.)

If you call -readString immediately after connecting, the whole welcome message might not be available yet.  We should really wait until an entire line of text has arrived.  One way to do this is to wrap the ONTCPSocket in an ONSocketStream.

    
ONSocketStream *socketStream = [ONSocketStream streamWithSocket:mySocket];


Then, we can send -readLine to the socketStream, which will read an entire line of text from the socket, up to the newline, blocking the thread until it has been fully received.

    
NSString *welcomeString = [socketStream readLine];


If you view the contents of welcomeString, you should see the greeting from the sendmail server.

**
Writing to the socket
**

Writing strings to the socket is just as easy as reading from it.  Refer to -writeString in the header files.  However, I'm not going to tell you how to write a sendmail client.  Not on this page anyway.  ;)

**
Disconnecting
**

To disconnect, simply release the socket.  If you used the ONSocketStream wrapper, you should release it first.

    
[socketStream release];
[mySocket release];


**
What if something goes wrong?
**

If OmniNetworking detects an error -- say the connection was refused, or the host name could not be resolved -- it will raise an exception, which you can catch with the standard Cocoa ExceptionHandling mechanism.  You can extract an idea of what went wrong by sending -reason to the caught exception.

    
NS_DURING
    /* your OmniNetworking calls here */
NS_HANDLER
    NSString* errorMsg = [localException reason];
NS_ENDHANDLER


**
But wait, there's more...
**

OmniNetworking offers many other goodies, such as UDP and Multicast sockets.  Given this primer, and the header files as reference, you should be able to figure them out.  And if you do, consider adding a page to this site about it!

-- StevenFrank

(See also: SmallSockets)


-- Eberhard Rensch

For people with problems to build the OmniNetworking.framework see: OmniNetworkingAndXCode

