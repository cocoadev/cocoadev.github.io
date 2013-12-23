---
layout: page
title: NSStream
---

Available in Mac OS X v10.3 and later.

NSInputStream and NSOutputStream are TollFreeBridged with CFReadStream and CFWriteStream.

----

Here's some code to get started with NSStream

    

// Header:
@interface StreamTest : NSObject {
    NSHost *host;
    int port;

    NSInputStream *inputStream;
    NSOutputStream *outputStream;
}


// Implementation:
- (void)open
{
        [NSStream getStreamsToHost:host 
                              port:port
                       inputStream:&inputStream
                      outputStream:&outputStream];
        
        [inputStream setDelegate:self];
        [outputStream setDelegate:self];
        
        [inputStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
                               forMode:NSDefaultRunLoopMode];
        [outputStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
                                forMode:NSDefaultRunLoopMode];
        
        [inputStream open];
        [outputStream open];
}

// Both streams call this when events happen
- (void)stream:(NSStream *)aStream handleEvent:(NSStreamEvent)eventCode
{
    if (aStream == inputStream) {
        [self handleInputStreamEvent:eventCode];
    } else if (aStream == outputStream) {
        [self handleOutputStreamEvent:eventCode];
    }
}

- (void)handleInputStreamEvent:(NSStreamEvent)eventCode
{
    switch (eventCode) {
        case NSStreamEventHasBytesAvailable:
            [self readBytes];
            break;
        case NSStreamEventOpenCompleted:
            // Do Something
            break;
        default:
        case NSStreamEventErrorOccurred:
            NSLog(@"An error occurred on the input stream.");
            break;
    }
}



I'll try to explain in more depth later -- PhilLarson

----

Here's more code. This is a sample implementation of a server using NSInputStream and NSOutputStream. Specifically, it makes it possible to use Foundation, not CFNetwork, for listening to a TCP port.

See NSStreamAdditions on http://homepage.mac.com/jrc/contrib/

- JohnChang

----

The following notes are credited to Mike Ash: Cocoa Guru.  They relate to the NSStreamAdditions code linked above.

The string methods are broken for non-ASCII data they will not work with strings that have non-ASCII characters for the following two reasons:


The obvious one is that he uses [string length] as the number of bytes, but it's the number of unichars, which is not the same for
                 anything except ASCII data

The subtle one is that individual unichars can get split across multiple bytes, and it's possible to receive the first part of a
                 unichar in one packet, and the last parts in another packet, or to have the first part lie at the end of your buffer, in which
                 case the data read from the stream in -readString won't be valid UTF-8 and the NSString initialization will fail

Finally the API also encourages you to think of writing one string, and reading one string but in fact it's entirely possible for -readString to return a partial string, or to return multiple sent strings concatenated together.

----

It's not only possible for this to happen, it's normal, from what I've seen. -- RobRix

----

Bug: 
This line:
    
    struct sockaddr_in sin = { .sin_family = AF_INET, .sin_port = port };   //...

Should be:
    
    struct sockaddr_in sin = { .sin_family = AF_INET, .sin_port = htons(port) };   //...

Doesn't make a difference on PPC, but it does on INTEL. -Jas

