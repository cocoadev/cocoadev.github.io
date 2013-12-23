---
layout: page
title: HowToSendBasicConnectToSocksProxy
---



Hi!

I'm trying to get my Jabberclient to connect to a Socks5 Proxy
I get the AsyncSocket to connect but then I'm supposed to send a 'Connect Packet' to the proxy with these settings:

    
Target Connects to StreamHost
CMD = X'01'
ATYP = X'03'
DST.ADDR = SHA1 Hash of: (SID + Initiator JID + Target JID)
DST.PORT = 0

StreamHost Acknowledges Connection
STATUS = X'00'


Heres my code for doing just this:

    
-(void) onSocket:(AsyncSocket *)sock didConnectToHost:(NSString *)host port:(UInt16)port
{
	NSLog(@"Socket Connected");

	NSString* stringToDigest = [NSString stringWithFormat:@"%@%@%@",_transferId, _to, _from];
	NSString* digest = [self generateProxyDigest:stringToDigest];
	
	NSString* sendString = [[NSString alloc] initWithBytes:"\5\1\3" length:3 encoding:NSUTF8StringEncoding];
	sendString = [sendString stringByAppendingString:digest];
	sendString = [sendString stringByAppendingString:[[NSString alloc] initWithBytes:"\0\0" length:2  encoding:NSUTF8StringEncoding]];
	
	[sock writeData:[sendString dataUsingEncoding:NSUTF8StringEncoding] withTimeout:-1 tag:0];

}


I can't get the proxy to send me anything back as its supposed, and I believe its something wrong with my code here...
All values are supposed to be sent in Hex format!
The returned digest is correct so it has to be in my encoding :/

Any ideas??

----
I don't know anything about this particular protocol, but in general when debugging these sorts of network problems, I always like to run WireShark, capture the stream sent by an app that works, capture the stream from my app, and then see what I'm doing differently.

