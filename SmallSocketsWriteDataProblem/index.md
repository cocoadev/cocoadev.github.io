---
layout: page
title: SmallSocketsWriteDataProblem
---

I am trying to write a DICT dictionary client using SmallSockets, and can connect to a DICT server properly, and retrieve the welcoming response from the server, all messages from the server I pop into a textview.

    
- (void)awakeFromNib
{
if ([self connectToServer] == YES)
	[outputTextField insertText:@"Connected"];
else
	[outputTextField insertText:@"Connection error"];
[self listenToServer];
}

- (BOOL)connectToServer
{
/* Set up a connection to the Dict.org server */
NS_DURING	

theSocket = [Socket socket];
[theSocket connectToHostName:@"dict.org" port:2628];

NS_HANDLER
	[outputTextField insertText:[localException description]];
	[outputTextField insertText:@"\n"];
	return NO;
NS_ENDHANDLER

return YES;
}

- (BOOL)listenToServer
{
response = [[NSMutableData alloc] init];

[theSocket readData:response];
        
// Display response in context textview
responseString = [[NSString alloc] initWithData:response encoding:[NSString defaultCStringEncoding]];
        
[outputTextField insertText:@"\n"];
[outputTextField insertText:responseString];
[outputTextField insertText:@"\n"];

//Clean up
[response release];
[responseString release];
}


- (IBAction)sendButton:(id)sender
{
//send a command:
[theSocket writeString:@"DEFINE hello"];

//[self listenToServer];
}

- (void)dealloc
{
[theSocket release];
}



We start at awakeFromNib - call connectToServer(), which will init the smallSocket object and connect it to the DICT server. That completes and we get back to awakeFromNib, which calls listenToServer() to listen to the welcoming reply.

There is a method sendButton: which is activated by a button on the interface.

I've looked into the writeString: method in SmallSockets, but I find that even if I comment out the entire content of the writeString: method (in the SmallSockets class) it still crashes!

If I do NOT listen at all to the server when it gives me its welcome after I connect, I can do a writeString:! So there must be something in listening that closes the socket or the object or something along those lines. There is indeed a call to close the socket in the listen method of SmallSockets that may be being called, but commenting out this does no different.

----

**You create the socket with     theSocket = [Socket socket];, and you don't retain it. If you don't alloc or copy an object, you must retain it if you want to keep it past the end of the current function or method.**

----

Now, my app hangs waiting to receive a response from the server, it hangs inside the SmallSocket class, when doing a readData: at this line:

 count = recv(socketfd, readBuffer, readBufferSize, 0);

----

 Still, I have a problem, the SmallSockets code says that the above line should return immediately if there is no data waiting - except it does not, it waits and waits.

----

I'm not familiar with SmallSockets, but it should be setting the socket to non-blocking using setsockopt() if that is the behavior it's supposed to have. If it's not doing this, then the recv() will block if there is no data like you're seeing.

----

Sometimes, I can attempt to read from my socket too quickly, before the server has a chance to respond, my client thinks there is nothing there, and continues without waiting, ignoring the servers response until the next time it checks the buffer.

I simply wait until the client at least STARTS receiving data from the server before allowing it to continue the app when it checks the buffer and finds nothing. Sometimes the server can't fill the buffer fast enough, and so halfway through the server's response, my app thinks the server is finished and so continues when it really should wait a bit.

At the moment I have the following setup, where I give the server a number of 'graces' where it can get away with my client getting an empty buffer, after each grace I give it a short amount of time to fill, and then start again, if at any time the client does receive data, the number of 'graces' is reinstated, so only, say, 5 consecutive empty buffer checks, with a half second between each, will prompt my client to go on.

Eg.

    
int bitsRetrieved = 0;
int emptyRetrieves = 5;

while (emptyRetrieves > 0)
	{
	bitsRetrieved = [theSocket readData:response];
	if (bitsRetrieved == 0)
		{
		sleep(0.6);
		emptyRetrieves--;
		}
	else
		emptyRetrieves = 5;
	}


Must I parse my data in order to determine whether some is missing, and then go back to the buffer?

----

When you're writing network code always assume that the absolute worst can happen. For example, in your situation, any of the following could happen:

*The server could send all but one character of the response, and then die.
*The server could send all but one character of the response right away, and then send the rest three minutes later.
*The server could start sending you 10GB of data from /dev/urandom.
*The server could send you data that looks legitimate but is corrupted in subtle ways.
*The server could not respond at all.


Your program should deal gracefully with all of these, and any other pathological scenarios you can come up with.

SmallSockets should provide a way to get a notification or callback when new data arrives. You can read the data from there. That will solve your current problem. As you can see, sleeping for arbitrary amounts of time is not going to work in the general case at all. As for taking care of the rest of these potential problems, well, that's what makes network programming so fun!

----
I have a question regarding general socket use. If you're behind a network, and you use a program such as AIM or whatever, how does the router/AIM server know exactly which computer to go to on the network. Let's say there are multiple people on AIM...don't they all show up as the same IP address to the AIM server? I've never known how that works...someone want to try to clue me in?

*Look up Network Address Translation, which is how these routers work.*

----

Port Address Translation is a better description of the "NAT" that routers are doing these days.

The end point of an IP connection is made up of 2 pieces of information: an IP address and port number. When a client connects to a server, that connection is distibguished the 2 end points: the server's IP, the server's port number, the client's IP, the client's port number. Every packet sent across the Internet has these 4 pieces of information inside it, so the receiving system knows what connection it's part of. (The IP stack in the operating system knows what connection should be delivered to what socket in what process...)

When you make a connection to a server from behind an AirPort that's doing "NAT", the AirPort receives packets from you with your internal address in them (10.0.1.2) and a port on your computer (19872). It then edits the packet so that it appears to be coming from the AirPort itself (but it's real IP address : 67.1.2.209) before sending it out to the world. When the AirPort gets a packet, it looks at the destination port number, looks that up in a table, and then changes the packet to have your internal address and port number and sends it on to you.

Creating a socket using the standard BSD socket() and connect() calls, and then wrapping it with an NSFileHandle works really well. I've never used SmallSockets, but I can't imagine it being much easier than NSFileHandle. You can even use NSSocketPort instead of socket() and connect() to create the socket if you don't want to get your hands dirty.

