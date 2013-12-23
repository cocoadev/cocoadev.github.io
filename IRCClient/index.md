---
layout: page
title: IRCClient
---

Ok, I have made my simple IRC Client. It has 2 NSTextFields (serverHost & serverPort) and NSTextView (clientConsole) and a pushbutton (Connect)

I have made a sub class of NSObject called "Controller" with 3 outlets (for the text field/views) and an action (connect:). I have linked the Connect button to the action connect:

The connect action uses smallsockets to connect to the specified irc serer/port. I made a loop like this:

responseSize = [response length];
    
    while ( responseSize > 0 ) {
        /* Read data from server */
        [socket readData:response];
        
        /* Translate data into ResponseString */
        responseString = [[[NSString alloc] initWithData:response encoding:[NSString defaultCStringEncoding]] autorelease]; 
    
        /* Update Console output */
        [clientConsole insertText:responseString];
        [clientConsole insertText:@"\n"];
    }


This reads the incomming data from the server and outputs it to the clientConsole (NSTextView). Though the problem is that It cuts out after a 30-sec timeout because my client doesn't reply to the server pings. Also it only updates the clientConsole (NSTextView) after the while statement. Is there anyway to refresh the clientConsole every while loop... like something at the end of the loop like refreshControl(clientConsole);

Any help what so every would be appreciated..

P.S. I once heard something about notifications in cocoa or something that might ba able to help me...

anway Thanks in advance!!

----
IMHO the problem is that you have your GUI in the same thread as the read loop. You must separate it to threads to allow GUI to update. This is a code I use in my own network app:

@implementation NetworkEngine
// here must be some inicialiazation etc.

- (void)readData:(id)sender userInfoDict:(NSDictionary*)aDict
{
    [NSThread detachNewThreadSelector:@selector(read:) toTarget:self withObject:aDict];

}

 
- (void)read:(NSDictionary*)anArgument
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    NSMutableData	*response = [[NSMutableData dataWithData:[socket readDataToEndOfFile]] retain];

	
	
#ifdef DEBUG
	[[NSNotificationCenter defaultCenter] postNotificationName:@"data received" object:response];
#endif


	[response release];
	
	[pool release];

    [NSThread exit];
}

-- This code will read data to the end and than exit. If you need something like a neverending loop, use this:


- (void)listen:(id)sender
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    NSData	*response = [NSData data];


    do {response = [broadcastSocket availableData];

        //post notification
        [[NSNotificationCenter defaultCenter] postNotificationName:@"data received" object:response];


#ifdef DEBUG

		NSLog(@"%@", response);

#endif

        //give a chance others
        [NSThread sleepUntilDate:[NSDate dateWithTimeIntervalSinceNow:0.5]];

    } while  (listening);


HTH

--BobC

