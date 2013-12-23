---
layout: page
title: HelpWithNSStreams
---

I'm a first-time cocoadev user, so please bear with me.  (My name's Adam Lang, in case anyone cares.)

I'm using NSStream's class method 'getStreamToHost:port:inputStream:outputStream' to get streams to and from a remote host. From all indications, that's what they're for: the documentation says: 

'You can create streams for data located in memory, in a file, or on a network (using sockets).' 

Well, fine. I make an NSHost, I set up the streams, everything seems fine. I open them both, and they both return the NSStreamEventOpenCompleted, but they aren't actually opened as socket connections. (According to netstat, anyway.) I then get a NSStreamEventHasSpaceAvailable for the send stream, but never get an NSStreamEventHasBytesAvailable for the receive stream, even though the first thing that should happen is that the receive stream should see some bytes shoved down it from the other side. (I'm currently connecting to a debug telnetd, so I should get 'login:' first thing.) 

And if I try to send some data through the send stream, I get a 'NSStreamEventErrorOccurred' event. If I ask it what kind of error, I get back: 

POSIX error: Socket operation on non-socket 

Now, that seems pretty odd. I mean, if you create a NSOutputStream and open it, and it says it has bytes available, I can't think of too many other things one would want to do to it other than write:maxLength:. But when I do that, not only does it not work, it tells me that I'm not even operating on a socket. Well, fair enough, maybe I inadvertantly created some other kind of stream besides a socket stream, but if that's the case then why are you using a socket write call to write to it?

Stranger still, if I look at netstat when the connections are (ostensibly) open, but before I've caused an error, there aren't any connections in the network part of the listing. However, in the 'Active LOCAL (UNIX) domain sockets' area, there are four new listings, two streams and two datagrams. Now, I don't really know how to parse those lines, but it seems likely to me that for some reason even when I specify a remote host in the connection NSHost, I'm creating some sort of local read and write streams. But why? That I couldn't tell you. 

Does anyone know what I'm doing wrong here?  If it would help, I'll post some of the code.  (By the way, I checked out NSStream and my code looks startlingly similar to that snippet.)

----

The code would probably help. But maybe NSHost isn't resolving the host-name you provide?

You could also try using one of the already-written socket classes, like AsyncSocket.

----

I am seeing exactly the same problem, so if anyone has any suggestions, please post them here. I would very much appreciate it.

Adam, have you managed to resolve the problem? If so, could you please put a quick note on here detailing what you had wrong and what you did to correct it.

----

I whipped up a quick and dirty working example. Even though this works in my case, it should be noted that Telnet is not always a simple *text* based protocol.

- NeilVanNote

----

I figured out what I was doing wrong, at least.  Duh.  I wasn't initializing the host at all, let alone initializing it with the value I thought I was.  I was setting the hostname, and had written 'setHostByName' so long ago that I hadn't noticed that I had in a comment in there // THIS SHOULD SET THE HOST AS WELL AS JUST STORING THE HOSTNAME.

I am such an idiot sometimes.

-- Adam Lang

----

    
@implementation AppDelegate

- (void)applicationDidFinishLaunching:(NSNotification *)notification
{
	NSHost *host = [NSHost hostWithName:@"somehost.somenet.dom"];
	if (nil != host)
	{
		[NSStream getStreamsToHost:host port:23 inputStream:&m_inStream outputStream:&m_outStream];
		
		if (nil != m_inStream && nil != m_outStream)
		{
			[m_inStream scheduleInRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];
			[m_outStream scheduleInRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];
		
			[m_inStream setDelegate:self];
			[m_outStream setDelegate:self];
			
			[m_inStream open];
			[m_outStream open];
		}
	}
}

- (void)stream:(NSStream *)theStream handleEvent:(NSStreamEvent)streamEvent
{
	NSString *io;
	
	if (theStream == m_inStream)
		io = @">>";
	else
		io = @"<<";
	
	NSString *event;
	switch (streamEvent)
	{
		case NSStreamEventNone:
			event = @"NSStreamEventNone";
			break;
		case NSStreamEventOpenCompleted:
			event = @"NSStreamEventOpenCompleted";
			break;
		case NSStreamEventHasBytesAvailable:
			event = @"NSStreamEventHasBytesAvailable";
			if (theStream == m_inStream)
			{
				uint8_t buffer[128];
				int len;
				while ([m_inStream hasBytesAvailable])
				{
					len = [m_inStream read:buffer maxLength:sizeof(buffer)];
					if (len > 0)
					{
						NSString *output = [[NSString alloc] initWithBytes:buffer length:len encoding:NSASCIIStringEncoding];
						if (nil != output)
						{
							NSLog(@"%@", output);
							[output release];
						}
					}
				}
			}
			break;
		case NSStreamEventHasSpaceAvailable:
			event = @"NSStreamEventHasSpaceAvailable";
			break;
		case NSStreamEventErrorOccurred:
			event = @"NSStreamEventErrorOccurred";
			break;
		case NSStreamEventEndEncountered:
			event = @"NSStreamEventEndEncountered";
			break;
		default:
			event = @"** Unknown";
	}

	NSLog(@"%@ : %@", io, event);
}

@end

