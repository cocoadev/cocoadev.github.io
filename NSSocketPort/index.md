---
layout: page
title: NSSocketPort
---

First, know that NSSocketPort is not for TCP/IP or UDP or anything like that - It is for DistributedObjects.

To do "regular" networking, you want to use the SocketClasses, CFNetwork, or NSStream.

----
Then why do the docs say it can be used either for DistributedObjects or raw messaging?

----
I think by "raw messaging," they mean direct use of NSPortMessage. That's no good if you want to work with HTTP or other specific protocols.

----
The above entries are exceedingly misleading: in truth, NSSocketPort is a great way to setup sockets quickly, using any protocol and family supported by the low-level UNIX socket APIs. However, NSSocketPort doesn't provide the required facilities itself to actually read and write data into the communications channel: for that, your best bet is to wrap the native file handle (obtained from [NSSocketPort socket]) in an NSFileHandle object and use the methods provided by the file handle wrapper class to communicate via the socket.

    
NSSocketPort* sock = [[NSSocketPort alloc] initWithTCPPort: 80 host: @"http://www.cocoadev.com"];
NSFileHandle* sHandle = [[NSFileHandle alloc] initWithFileDescriptor: [sock socket] closeOnDealloc: YES];


----
You can also trivially create a server socket using the above technique:


    
  NSSocketPort * serverSock = [[NSSocketPort alloc] initWithTCPPort: 8081];
  NSFileHandle * socketHandle = [[NSFileHandle alloc] initWithFileDescriptor: [serverSock socket]
                                                              closeOnDealloc: YES];
  [socketHandle acceptConnectionInBackgroundAndNotify];
  [[NSNotificationCenter defaultCenter] addObserver: self selector: @selector(notified:) 
                                               name: NSFileHandleConnectionAcceptedNotification
                                             object: socketHandle];


Why isn't this in Apple's documentation?

----

An undocumented "feature": if you attempt to use initRemoteWithTCPPort:host: with an invalid or unavailable host name, the result is nil.  If you then try to get a file handle, [nil socket] will return 0, and you will end up with a handle for your machine's STDIN.  Since this depends on the network connection status of the machine -- not just on the correctness of the arguments -- you should *always* verify that the result is non-nil.

----
The above client code is wrong.  First, the method is named "initRemoteWithTCPPort:host:", not "initWithTCPPort:host:".  Second, a host string must just be a server name or IP address, not a URL.  "www.cocoadev.com" would be appropriate.  Third, whenever I try to run it, the file descriptor I get from [serverSock socket] is -1.  I think my problem is related to this note in Apple's documentation: "A connection is not opened to the remote host until data is sent."  In other words, I *think* you must use the NSSocketPort to send something (probably a non-trivial process not designed for typical users' consumption) before [serverSock socket] will give you a useful result.

I would fix the example myself (perhaps remove it completely), except that I'm not sure whether there is no simple way to get a file handle from an NSSocketPort, so I don't want to mess with it.  Presumably the poster has gotten code like this to work?

----
For a good complete example on using NSSocketPort (or, rather, if you read the whole thing, don't use it is the conclusion) in server code, read http://www.macdevcenter.com/pub/a/mac/2006/11/14/how-to-write-a-cocoa-web-server.html

----
Another argument against using NSSocketPort for your networking (setting aside that it wasn't designed for it) is that it doesn't exist on the iPhone.

----
A question because of converting CF calls to Cocoa. How can I create a NSSocketPort from an existing fileDescriptor and get the ip address of the corresponding host?

----
This is very similar to acceptor and reactor in Ace Framework.

