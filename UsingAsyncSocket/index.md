---
layout: page
title: UsingAsyncSocket
---



I'm sure I'm missing something simple here, but how do I know that there is data to be read?
Thanks.

----

You don't. You tell it that you are expecting data and *want* to read it, and then your delegate gets called when the data is *done* being read.

But if you really need to know if data is available, you can call     General/CFReadStreamHasBytesAvailable([asyncSocket getCFReadStream]). Note that if General/AsyncSocket has an outstanding read request, it will be slurping up the data as soon as it arrives, so this call will only be useful before you make a read request.

I guess the documentation isn't quite clear enough�

----

In order to get an understanding of how General/AsyncSockets work I wrote two small projects, a client and a server, that connect and communicate with one another.
Everything seems to work fine when I run them both on the machine on which they were created.  When I compile (for release) the client and take it to another machine (on the same network and subnet) they don't communicate.  Further, the client app crashes after a minute or two.

The apps are both very basic and don't do anything apart from talking to each other.

Specifics:
both machines are running 10.4.2
General/XCode 2.1
General/AsyncSockets 4.1

Thanks for looking.

*Care to post the source?*

    
#import "General/MyObject.h"

@implementation General/MyObject

- (void)awakeFromNib
{
	testSocket = General/[[AsyncSocket alloc] initWithDelegate: self];
}

- (General/IBAction)makeConnection:(id)sender
{
	[testSocket connectToHost: @"10.0.4.15" onPort: 2001 error: nil];
}

- (General/IBAction)send:(id)sender
{
	General/NSData *dataToSend;
	General/NSString *stringToSend;
	
	stringToSend = @"this is a test";
	
	dataToSend = [stringToSend dataUsingEncoding: General/NSASCIIStringEncoding];
	[testSocket writeData: dataToSend withTimeout: -1 tag: 0];
	
}

#pragma mark -
#pragma mark General/AsyncSocket Delegate Methods

- (void)onSocket:(General/AsyncSocket *)sock didConnectToHost: (General/NSString *)host port:(UInt16)port
{
	General/NSLog(@"we seem to have made a connection!");
}

- (void)onSocket:(General/AsyncSocket *)sock didWriteDataWithTag:(long)t
{
	General/NSLog(@"data written!");
	[sock readDataWithTag: 0];
}

- (void)onSocket:(General/AsyncSocket *)sock didReadData:(General/NSData *)data withTag:(long)t
{
	General/NSString *messageString = General/[[NSString alloc] initWithData: data encoding: General/NSASCIIStringEncoding];
	General/NSLog(@"received message:  %@", messageString);
}
@end


----

Did you get this figured out?

----
I'll tell you why General/AsyncSocket doesn't cut it for me.  It works fine if you know what data to expect.  It does not work well if you are listening for arbitrary data at arbitrary times.  I want to read any data that comes in, and react to it.  My three choices for reading data:

"read-to-length": I can't do this, I don't know what data I'm going to receive.

"read-to-data": I can't do this, I don't know what data I'm going to receive.

"read-all": this is my only option.  But the problem is that it returns immediately.  Most of the time, it returns empty, so I have to call "read-all" again.  Lather, rinse, repeat.  If I just call "read-all" again, then my process takes up 100% CPU in this do-nothing cycle.  Or I could put it on a timer, and check for new data every so often.  But what I really want, and General/AsyncSocket doesn't provide, is a notification that the socket has received data and that I should deal with it.  Because it knows when I've received data better than I do.

This, I might note, is something that General/NetSocket deals with perfectly well....  In general, I do like General/AsyncSocket better, but this is a big shortcoming.

----
You could try asking it to read a single byte, and then do a 'read-all', which should get you whatever else came in with that single byte. It's kind of crappy, but it would work.

----
I've added the requested functionality in 4.3, now up. It *was* pretty much a waste to have empty data. � General/DustinVoss

----

I have also tried using General/ASyncSocket which in some cases can be very nice and convenient.  but I have found that I get more
control and more reliable results (ie less partial messsages) by simply opening a normal socket connection,
spawning a new thread, and using select() to check if there is new data there to be read...  
General/AsyncSockets are super-convenient and very easy to use...  which in and of itself implies performance drawbacks...  my $0.02  |K<
