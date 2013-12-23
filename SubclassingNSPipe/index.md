---
layout: page
title: SubclassingNSPipe
---



Hello my dear comrades.

I'm trying to create a General/NSPipe subclass that automatically reads everything written to it into a supplied General/NSMutableString.
    The problem is that as soon as i call     [self fileHandleForReading] my whole call stack disappears (I'm not 100% sure of the terminology here), and i end upp in some obscure function called by General/NSNotificationCenter (    _nsnote_callback()).
The code is written like this:

    

@interface General/ReadOutputPipe : General/NSPipe {
    General/NSMutableString *output;
    General/NSArray *modes;
    
    General/NSFileHandle *fileHandle;
}

#pragma mark Initializers

+ (id)pipeWithReadOutput:(General/NSMutableString *)outputString forModes:(General/NSArray *)runLoopModes;
- (id)initWithReadOutput:(General/NSMutableString *)outputString forModes:(General/NSArray *)runLoopModes;


#pragma mark General/NSFileHandle Notifications

- (void)fileHandleDidCompleteReading:(General/NSNotification *)aNotification;

@end

@implementation General/ReadOutputPipe

#pragma mark Initializers

+ (id)pipeWithReadOutput:(General/NSMutableString *)outputString forModes:(General/NSArray *)runLoopModes {
    return General/[self alloc] initWithReadOutput:outputString forModes:runLoopModes] autorelease];
}
- (id)initWithReadOutput:([[NSMutableString *)outputString forModes:(General/NSArray *)runLoopModes {
    if (self = [super init]) {
        output = [outputString retain];
        modes = [runLoopModes copy];
        
        fileHandle = [self fileHandleForReading];
        
        [notificationCenter addObserver:self
                               selector:@selector(fileHandleDidCompleteReading:)
                                   name:General/NSFileHandleReadCompletionNotification
                                 object:fileHandle];
        
        [fileHandle readInBackgroundAndNotifyForModes:modes];
    }
    
    return self;
}
- (void)dealloc {
    [notificationCenter removeObserver:self];
    
    [output release];
    [modes release];
    
    [super dealloc];
}


#pragma mark General/NSFileHandle Notifications

- (void)fileHandleDidCompleteReading:(General/NSNotification *)aNotification {
    General/NSData *readData = General/aNotification userInfo] valueForKey:[[NSFileHandleNotificationDataItem];
    
    // an empty data object means EOF, so until we get that we just keep requesting more
    //
    if ([readData length]) {
        General/NSString *latestOutput = General/[[[NSString alloc] initWithData:readData encoding:NSUTF8StringEncoding] autorelease];
        
        [output appendString:latestOutput];
        
        [fileHandle readInBackgroundAndNotifyForModes:modes];
    }
}

@end



All help is welcomed.

/Mr Fisk

----

Actually, looking it through in the debugger yet again, I see that the call stack is just reverted back to just before my     General/[[NSApp delegate] applicationWillFinishLaunching:] method is called, but there's no clues whatsoever in the debugger console.

/Mr Fisk

----

It seems like a very odd choice to make this an General/NSPipe subclass. This seems like the right time to write something with a has-a relationship, where you write a custom class which has an General/NSPipe instance variable, rather than an is-a relationship where you subclass.

In any case, can you post the stack trace and tell us which line the crash occurs on, if it is indeed crashing? If it's not crashing, what exactly is the problem?

----

The reason for making it a subclass of General/NSPipe is that then you can pass it to, for example,     -General/[NSTask setStandardOutput:] and then just parse the string. I don't really see how this is bad design, since the original function of General/NSPipe is still maintained, but you get a General/NSMutableString instead of the General/NSFileHandle.
Like I explained before, it doesn't crash the application, but it jumps back in the call stack to where     General/[[NSApp delegate] applicationWillFinishLaunching:] gets called, with no error logs at all. I tried stepping into     -[self fileHandleForReading], but it still does the same thing.

----

Are you sure that this is an actual program flow problem and not just a debugger problem? Have you tried inserting General/NSLog statements to see exactly what the program flow is like? What does your applicationWillFinishLaunching: method look like?

----

Ah, got it now, General/NSPipe is an abstract class etc etc..
seems like xCode 2.1 doesn't print log output to the debugger console like the older versions used to. Because they did use to do that, right? I haven't done any coding for a couple of months, so it might just be me being stupid.

*Used to and still does. You're either doing something wrong, or your Xcode is broken. Which window the output appears in depends on whether you're running or debugging; are you sure you're looking at the right one?*

Anyway, the docs don't say anything about General/NSPipe being an abstract class, they should right? And, if they are in fact lacking, you're supposed to file a bug report?

*Yes they should, and yes you should. The easiest way is probably to just use the feedback section at the bottom of the docs page, which is what I'm going to do.*

----

Just for public interest, here's the final code for the class. It doesn't work in quite the same way, since i realized that you can't convert the read data to a utf8-encoded string due to variable length characters (what if you just got the first byte of two?)

    

@interface General/WritableBufferPipe : General/NSPipe {
    int readDescriptor;
    General/NSFileHandle *writeHandle;
    
    General/NSMutableData *buffer;
    General/NSLock *bufferLock;
}

#pragma mark Actions

- (void)readDataToEndOfFile;


#pragma mark Accessors

- (General/NSData *)buffer;

@end

@implementation General/WritableBufferPipe

#pragma mark Initializers

+ (id)pipe {
    return General/[self alloc] init] autorelease];
}
- (id)init {
    if (self = [super init]) {
        int fileDescriptors[2];
        
        if (0 != pipe(fileDescriptors)) {
            [self release];
            return nil;
        }
        
        readDescriptor = fileDescriptors[0];
        
        writeHandle = [[[[NSFileHandle alloc] initWithFileDescriptor:fileDescriptors[1]
                                                    closeOnDealloc:YES];
        
        buffer = General/[[NSMutableData alloc] init];
        bufferLock = General/[[NSLock alloc] init];
        
        General/[NSThread detachNewThreadSelector:@selector(readDataToEndOfFile)
                                 toTarget:self
                               withObject:nil];
    }
    
    return self;
}
- (void)dealloc {
    close(readDescriptor);
    [writeHandle release];
    
    [buffer release];
    [bufferLock release];
    
    [super dealloc];
}


#pragma mark Actions

- (void)readDataToEndOfFile {
    General/NSAutoreleasePool *autoreleasePool = General/[[NSAutoreleasePool alloc] init];
    
    char readBuffer[1024];
    int bytesRead = 0;
    
    while (bytesRead = read(readDescriptor, readBuffer, sizeof(readBuffer))) {
        [bufferLock lock];
        
        [buffer appendBytes:readBuffer length:bytesRead];
        
        [bufferLock unlock];
    }

    [autoreleasePool release];
}


#pragma mark Accessors

- (General/NSData *)buffer {
    [bufferLock lock];
    
    General/NSData *bufferSnapshot =  General/buffer copy] autorelease];
    
    [bufferLock unlock];

    return bufferSnapshot;
}

- (id)fileHandleForReading {
    [[[NSException raise:General/NSInvalidArgumentException
                format:@"-fileHandleForReading cannot be sent to an instance of %@.", [self className]];

    return nil;
}

- (id) fileHandleForWriting {
    return writeHandle;
}

@end



/Mr Fisk

----

Thanks for posting the code, it looks nice. If you really needed the data to stream, rather than waiting for it all to accumulate, it wouldn't be that difficult to write a very basic UTF-8 parser that's just smart enough to know where the character breaks lie. Of course, if you don't need streaming, then there's no reason to go through that work.
