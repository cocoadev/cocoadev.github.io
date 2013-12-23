---
layout: page
title: CatchWriteDataException
---

I am using NSTask to pipe data through a command, the code is like this:
    
@implementation NSData (AsyncWrite)
- (void)writeToFilehandle:(NSFileHandle *)fh
{
   NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
NS_DURING
   [fh writeData:self];
NS_HANDLER
   NSLog(@"Exception during write");
NS_ENDHANDLER
   [fh closeFile];
   [pool release];
}
@end

   ...
   NSTask *cmd = [[NSTask alloc] init];
   NSPipe *in = [NSPipe pipe], *out = [NSPipe pipe];
   [cmd setLaunchPath:@"/bin/tcsh"];
   [cmd setArguments:[NSArray arrayWithObjects:@"-c", command, nil]];
   [cmd setCurrentDirectoryPath:[@"~/" stringByExpandingTildeInPath]];
   [cmd setStandardInput:in];
   [cmd setStandardOutput:out];
   [cmd launch];

   // write to command in separate thread
   [NSThread detachNewThreadSelector:@selector(writeToFilehandle:)
      toTarget:[NSData dataWithBytes:&bytes[0] length:bytes.size()]
      withObject:[in fileHandleForWriting]
   ];

   // read result from command
   NSData *res = out fileHandleForReading] readDataToEndOfFile];
   ...


The code is sort of okay, the problem arise when a command doesn't read from standard in, and there is a lot of data (probably > 8KB), then I get an exception from writeData:, I do catch this exception (i.e. my [[NSLog is invoked), but my program will hang afterwards. It hangs in the readDataToEndOfFile selector.

...

Okay, I have sort of solved the problem -- despite the exception, I also get a SIGPIPE (http://www.wlug.org.nz/SIGPIPE) and it seems the default handler for this signal is 'broken' (in that it makes my app hang), so I install my own handler using:
    
signal(SIGPIPE, BrokenPipeHandler);

--AllanOdgaard


----

why don't you try reading in background?

*Because the code which fails is the code which **write** to the task -- furthermore, it would complicate my program design a great deal, since the program should be "blocked" while waiting for the result of the NSTask.*

    

    ...
    id outputPipe=[NSPipe pipe];
    taskOutputHandle=[outputPipe fileHandleForReading];  // taskOutputHandle is an instance variable
    [yourTask setStandardOutput:outputPipe];
    [outputHandle readInBackgroundAndNotify];
    ...

-(void)methodThatRespondsToNSFileHandleDataAvailableNotification:(id)sender {
    if (taskOutputHandle==sender) {
        blah, blah, blah.......
    }
}

-(id)init {
    ...
    [[NSNotificationCenter defaultCenter] addObserver:self 
        selector:@selector(methodThatRespondsToNSFileHandleDataAvailableNotification:)
        name:NSFileHandleDataAvailableNotification
        object:nil];
    ...
}

-(void)dealloc { 
    [[NSNotificationCenter defaultCenter] removeObserver:self]; 
    [super dealloc];
}


