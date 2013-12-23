---
layout: page
title: NSPipe
---

An object-oriented interface for accessing pipes usually used with NSTask to redirect task output to a Cocoa UI element.

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSPipe_Class/index.html


A "pipe" is a communications channel that links one process to another.  Data written on one of the pipe by one program can be read by a second program.  Pipes are unidirectional.  To pass data back and forth between two programs, you'd use two pipes.  Whenever you link commands in the shell (like "ls -lat | head"), you're using a pipe under the hood to move data around.

----

"The data that passes through the pipe is buffered; the size of the buffer is determined by the underlying operating system."

from: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSPipe_Class/index.html

The NSPipe buffer limit seems to be 4096 bytes (cf. /usr/include/limits.h: "... #define _POSIX_ARG_MAX          4096 ...")

And also see this related issue: Max arguments for NSTask? ( http://www.cocoabuilder.com/archive/message/cocoa/2002/10/27/66713 )

Here's an Xcode Foundation Tool example: 

<source lang="objc">
int main (int argc, char *argv[]) {
	NSAutoreleasePool *pool = [NSAutoreleasePool new];
 
	NSString *file = @"/path/to/input/file";   
 
	NSData *data1 = [NSData dataWithContentsOfFile:file]; 
 
	printf("data1 bytes: %i\n", [data1 length]);    // should be > 4096 
 
	//NSRange range = {0, [data1 length]}; 
	//NSRange range = {0, 4097};  
	NSRange range = {0, 4096};
 
	NSData *data2 = [data1 subdataWithRange:range]; 

	printf("data2 bytes: %i\n", [data2 length]);   
 
	NSPipe *inPipe = [NSPipe pipe];
	NSFileHandle *fh = [inPipe fileHandleForWriting];  
	[fh writeData: data2];  
	[fh closeFile];
 
	NSPipe *outPipe = [NSPipe pipe];
 
	NSTask *task = [NSTask alloc] init] autorelease];
	[task setStandardInput:inPipe];   
	[task setStandardOutput:outPipe];
	[task setLaunchPath:@"/bin/cat"]; 
	NSArray *args = [NSArray arrayWithObjects:@"-n", nil];   
	[task setArguments:args];
	[task launch];                                                                     
 
	NSData *output = [[outPipe fileHandleForReading] readDataToEndOfFile];
	NSString *string = [[[NSString alloc] initWithData:output encoding:NSUTF8StringEncoding] autorelease];
 
	NSLog(@"\n%@\n", string);
 
	[pool drain];
	return 0;
}
</source>

----

Another use for [[NSPipe is to capture data from a method which requires a file handle (FILE*) into an NSData object. This is useful for interacting with a lot of pesky non-objective-c  libraries:

<source lang="objc">

+(NSData*)captureFileStreamIntoDataObject {
  NSPipe* thePipe = NSPipe alloc] init];  
  [NSThread detachNewThreadSelector:@selector(backgroundThread:) toTarget:self withObject:[thePipe fileHandleForWriting;  
  NSData* theData = nil;
  NSMutableData* theMutableData = [NSMutableData data];
  while((theData = thePipe fileHandleForReading] availableData]) && [theData length]) {
    [theMutableData appendData:theData];
  }
  [thePipe release];
  return theMutableData;
}

+(void)backgroundThread:(NSFileHandle*)theFileHandle {
  NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];
  FILE* theFilePointer = fdopen([theFileHandle fileDescriptor], "w");   

  commandWhichWritesToFile(theFilePointer);

  fclose(theFilePointer);
  [theFileHandle closeFile];
  [pool release];
}

</source>

- [[DavidThorpe

----

<source lang="objc">

/*

Sample code showing how to write more than 64 KB to the stdin of an NSTask using NSPipe & pthread.

# this command will be rewritten in Objective-C using an NSTask to execute sh -c '...'
cat testfile.txt | sh -c 'cat -n | tail -n 1'   

compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -lpthread -o nspipe_pthread_writedata nspipe_pthread_writedata.m

./nspipe_pthread_writedata


# create testfile.txt
jot -b "line" 10000000 > testfile.txt   # 48M
#jot -b "line" 50000000 > testfile.txt   # 238M
echo 'Hello, world!' >> testfile.txt
stat -f "%z bytes" testfile.txt
du -h testfile.txt


References:

- "NSPipe (NSFileHandle) writedata limit?"  (April 09, 2010),
  http://www.cocoabuilder.com/archive/cocoa/284765-nspipe-nsfilehandle-writedata-limit.html,
  http://pastie.org/911738 (by Dave Keck)

- "64KB limit in passed in text for a service using NSTask" (August 20, 2010),
  http://www.cocoabuilder.com/archive/cocoa/292247-64kb-limit-in-passed-in-text-for-service-using-nstask.html

- http://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man3/pthread.3.html


Todo:

- Do the same without using pthread (or threading at all).

*/

// This sample code is based on a code snippet by Dave Keck, http://pastie.org/911738.

#import <Foundation/Foundation.h>
#import <pthread.h>
#import <stdio.h>

static NSMutableString *outString = nil;

void *threadFunction(NSPipe *pipe)
{

   NSAutoreleasePool alloc] init];

   NSPipe *outPipe = [[NSPipe alloc] init];
   NSTask *task = [[NSTask alloc] init];

   NSString *cmdstring = @"/bin/cat -n | /usr/bin/tail -n 1";
   NSArray *args = [[NSArray alloc] initWithObjects: @"-c", cmdstring, nil];   

   NSData *output = nil;
   NSString *str = nil;

   [task setStandardInput: pipe];   
   [task setStandardOutput: outPipe];
   [task setLaunchPath: @"/bin/sh"]; 
   [task setArguments: args];

   [task launch]; 

   outString = [[NSMutableString alloc] init];

   while ( (output = [[outPipe fileHandleForReading] availableData]) && [output length] ) 
   {
      str = [[NSString alloc] initWithFormat:@"%.*s", [output length], [output bytes;
      [outString appendString: str];
      [str release];
   }

   [task release];
   [args release];
   [outPipe release];

   pthread_exit(outString);

}

int main(int argc, const char *argv[])
{

   NSAutoreleasePool alloc] init];

   NSString *file = @"testfile.txt";   

   void *outputString = nil;

   NSData *data = [[NSData alloc] initWithContentsOfFile: file]; 

   printf("file size: %i bytes\n", [data length]); 

   NSPipe *pipe = [[NSPipe alloc] init];

   pthread_t thread = nil;
   if (pthread_create(&thread, nil, (void* (*)(void*))threadFunction, pipe) != 0)
   {
      perror("pthread_create failed");
      return 1;
   }

   [[pipe fileHandleForWriting] writeData: data];
   [[pipe fileHandleForWriting] closeFile];
    
   // wait until thread has finished execution
   if (pthread_join(thread, &outputString) != 0)
   {
      perror("pthread_join failed");
      return 1;
   }

   //NSLog(@"\n%@", [outputString class]);

   NSString *newstr = [[NSString alloc] initWithString: outputString];
   NSLog(@"\n%@", newstr);

   [data release];
   [pipe release];
   [newstr release];
   [outString release];

   return 0;

}

</source>

----

<source lang="objc">

/*

ipctest.c by Dave Keck

compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -o ipctest ipctest.c

./ipctest

Source:
   "NSPipe (NSFileHandle) writedata limit?"  (April 09, 2010),
   http://www.cocoabuilder.com/archive/cocoa/284765-nspipe-nsfilehandle-writedata-limit.html,
   http://themha.com/ipctest (by Dave Keck)

*/

#import <Foundation/Foundation.h>
#import <unistd.h>
#import <stdio.h>
#import <pthread.h>
#import <assert.h>

#define DATA_LENGTH 0x100000
//#define DATA_LENGTH 0x10000000

int main(int argc, const char *argv[])
{

    NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];
    
    if (argc == 1)
    {
    
        /* Parent */
        
        NSPipe *pipe = nil;
        pid_t pid = 0;
        const char *readDescriptorString = nil;
        
        pipe = [NSPipe pipe];
        readDescriptorString = [[NSString stringWithFormat: @"%d", [[pipe fileHandleForReading] fileDescriptor UTF8String];
        fprintf(stdout, "readDescriptorString: %s\n", readDescriptorString);

        pid = fork();
        
            assert(pid >= 0);
        
        if (!pid)
        {
            /* Child */
            
            execl(argv[0], argv[0], readDescriptorString, (char *)NULL);
            //execl(argv[0], argv[0], readDescriptorString, nil);
            _exit(0);
        }
        
        /* Parent (continued) */
        
        void *primitiveData = malloc(DATA_LENGTH);
        NSData *data = [NSData dataWithBytes: primitiveData length: DATA_LENGTH];
        uint32_t dataLength = DATA_LENGTH;
        
        // Assuming that both processes share same endianness (bad idea)
        pipe fileHandleForWriting] writeData: [NSData dataWithBytes: &dataLength length: sizeof(dataLength);
        pipe fileHandleForWriting] writeData: data];
        
        NSLog(@"Parent: finished writing data, sleeping it off...");
        
        for (;;)
            sleep(-1);
    
    }
    
    else if (argc == 2)
    {
    
        /* Child (continued) */
        
        NSFileHandle *readFileHandle = nil;
        uint32_t dataLength = 0;
        NSData *readData = nil;
        
        readFileHandle = [[[NSFileHandle alloc] initWithFileDescriptor: atoi(argv[1])] autorelease];
        readData = [readFileHandle readDataOfLength: sizeof(dataLength)];
            assert(readData);
        [readData getBytes: &dataLength length: sizeof(dataLength)];
        
        readData = [readFileHandle readDataOfLength: dataLength];
            assert(readData);
        
        NSLog(@"Child: read correct amount of data: %@ (%d bytes)", ([readData length] == DATA_LENGTH ? @"yes!" : @"no!"), [readData length]);
    
    }
    [pool release];
    return 0;

}

</source>

----

<source lang="objc">

/*

asynctask.m  --  sample code that shows how to implement asynchronous stdin, stdout & stderr streams for processing data with NSTask

compile with:
gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -o asynctask asynctask.m

Before running ./asynctask you may want to create testfile.txt as shown below and customize the code of the main() function at the bottom before compilation.

./asynctask
./asynctask  > asynctask-output.txt 2>&1
for ((i=0; i < 20; i++)); do ./asynctask; done

open -e asynctask-output.txt


asynctask.m is based on and extends FRCommand.h and FRCommand.m by Torsten Curdt (Apache License, Version 2.0).

Being a GUI-less application (i.e. a Foundation-based command line tool), asynctask.m runs an NSRunLoop manually 
to enable the use of asynchronous "waitForDataInBackgroundAndNotify" notifications. In addition, asynctask.m 
uses pthread_create(3) and pthread_detach(3) for writing more than 64 KB to the stdin of an NSTask.

Source code:
- http://github.com/tcurdt/feedbackreporter (by Torsten Curdt; Apache License, Version 2.0)
- http://github.com/tcurdt/feedbackreporter/tree/master/Sources/Main/
- http://github.com/tcurdt/feedbackreporter/blob/443300c21f6b7c5b70298edd861b4b2017a97b00/Sources/Main/FRCommand.h
- http://github.com/tcurdt/feedbackreporter/blob/443300c21f6b7c5b70298edd861b4b2017a97b00/Sources/Main/FRCommand.m


# create testfile.txt
jot -b "line" 500 > testfile.txt   
jot -b "line" 20000 > testfile.txt  
jot -b "line" 1000000 > testfile.txt  
du -h testfile.txt

*/

/*
 * Copyright 2008, Torsten Curdt
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

//#import <Cocoa/Cocoa.h>

#import <Foundation/Foundation.h>
#import <unistd.h>
#import <pthread.h>
#import <stdio.h>


// ADDED  begin

static int empty_data_count;
static int task_exit_code;

struct arg_struct {
    NSData *inData;
    NSFileHandle *inHandle;
};

void *threadFunction(void *arguments)
{
   [[NSAutoreleasePool alloc] init];
   struct arg_struct *taskArgs = arguments;
   [taskArgs -> inHandle writeData: taskArgs -> inData];
   [taskArgs -> inHandle closeFile];
   //[taskArgs -> inHandle release];
   //[taskArgs -> inData release];
   return nil;
}

// ADDED  end


@interface FRCommand : NSObject {
   NSTask *task;
   NSString *path;
   NSArray *args;
   NSData *stdinData;   // ADDED
   NSMutableString *output;
   NSMutableString *error;
   BOOL terminated;
   NSFileHandle *outFile;
   NSFileHandle *errFile;
   NSFileHandle *stdinHandle;  // ADDED
}

-(NSData *) availableDataOrError: (NSFileHandle *)file;
- (id) initWithPath:(NSString*)path;
- (void) setArgs:(NSArray*)args;
- (void) setInput:(NSData*)stdinData;        // ADDED
- (void) setError:(NSMutableString*)error;
- (void) setOutput:(NSMutableString*)output;
- (int) execute;
@end

//#import "FRCommand.h"


@implementation FRCommand

- (id) initWithPath:(NSString*)pPath
{
   self = [super init];
   if (self != nil) 
   {
      task = [[NSTask alloc] init];
      args = [NSArray array];
      path = pPath;
      stdinData = nil;  // ADDED
      error = nil;
      output = nil;
      terminated = NO;
   }

   return self;

}


// ADDED
// For "availableDataOrError:" see: 
// - "NSTasks, NSPipes, and deadlocks when reading...",
//    http://dev.notoptimal.net/2007/04/nstasks-nspipes-and-deadlocks-when.html
// - "NSTask stealth bug in readDataOfLength!! :(", 
//    http://www.cocoabuilder.com/archive/cocoa/173348-nstask-stealth-bug-in-readdataoflength.html#173647

-(NSData *) availableDataOrError: (NSFileHandle *)file {
   for (;;) 
   {
      @try {
         return [file availableData];
      } @catch (NSException *e) {
         if ([[e name] isEqualToString:NSFileHandleOperationException]) 
         {
            if ([[e reason] isEqualToString: @"*** -[NSConcreteFileHandle availableData]: Interrupted system call"]) 
            {
               continue;
            }
            return nil;
         }
         @throw;
      }
   }  // for
}

- (void) setArgs:(NSArray*)pArgs
{
    args = pArgs;
}

- (void) setInput:(NSData*)pInput    // ADDED  
{
    stdinData = pInput;
}

- (void) setError:(NSMutableString*)pError
{
    error = pError;
}

- (void) setOutput:(NSMutableString*)pOutput
{
    output = pOutput;
}

-(void) appendDataFrom:(NSFileHandle*)fileHandle to:(NSMutableString*)string
{

   NSData *data = [self availableDataOrError: fileHandle];
   //NSData *data = [fileHandle availableData];
   if ([data length]) {
      NSString *s = [[NSString alloc] initWithBytes:[data bytes] length:[data length] encoding: NSUTF8StringEncoding];
      //NSString *s = [[NSString alloc] initWithBytes:[data bytes] length:[data length] encoding: NSASCIIStringEncoding];

      // ADDED  (originally only [output appendString: s];)
      // cf. http://www.karlkraft.com/index.php/2008/01/07/equality-vs-identity/
      if (fileHandle == outFile)
          [output appendString: s];
      else if(fileHandle == errFile)
          [error appendString: s];
      else
          NSLog(@"missing stdout / stderr error: fileHandle does not match outFile or errFile\n");

      //NSLog(@"| %@", s);    // uncomment for: /usr/bin/tail -f /path/to/test.log
      //fprintf(stderr, "\r\033[0K%s", [s UTF8String]);   // uncomment for: curl -L -O ...
      [s release];
      //[fileHandle waitForDataInBackgroundAndNotify];
   }else{
      empty_data_count += 1;
      if (empty_data_count > 10)
      {   
         //[task interrupt];   // failed to abort infinite NSRunLoop
         //[task terminate];   // same
         [[NSNotificationCenter defaultCenter] removeObserver:self];  // only way to abort infinite NSRunLoop ???
      }
   }

   [fileHandle waitForDataInBackgroundAndNotify];

}

-(void) outData: (NSNotification *) notification
{
    //NSLog(@"outData:\n");
    NSFileHandle *fileHandle = (NSFileHandle*) [notification object];
    [self appendDataFrom:fileHandle to:output];
    [fileHandle waitForDataInBackgroundAndNotify];
}

-(void) errData: (NSNotification *) notification
{
    NSFileHandle *fileHandle = (NSFileHandle*) [notification object];
    //[self appendDataFrom:fileHandle to: output];
    [self appendDataFrom:fileHandle to: error];   // ADDED
    [fileHandle waitForDataInBackgroundAndNotify];
}

- (void) terminated: (NSNotification *)notification
{
    NSLog(@"Task terminated");
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    terminated = YES;
}

- (int) execute
{
   empty_data_count = 0;

   [task setLaunchPath:path];
   [task setArguments:args];

   NSPipe *outPipe = [NSPipe pipe];
   NSPipe *errPipe = [NSPipe pipe];
   [task setStandardOutput:outPipe];
   [task setStandardError:errPipe];
   [task setCurrentDirectoryPath:@"."];  // ADDED

   //NSFileHandle *outFile = [outPipe fileHandleForReading];   // TROUBLEMAKER
   //NSFileHandle *errFile = [errPipe fileHandleForReading];
   outFile = [outPipe fileHandleForReading];
   errFile = [errPipe fileHandleForReading];

   //  ADDED
   // create inPipe after outPipe & errPipe
   NSPipe *inPipe = [NSPipe pipe];     
   stdinHandle = [inPipe fileHandleForWriting];

   [task setStandardInput:inPipe];

   struct arg_struct taskArguments;
   taskArguments.inData = stdinData;
   taskArguments.inHandle = stdinHandle;

   [[NSNotificationCenter defaultCenter] addObserver:self
                                            selector:@selector(terminated:)
                                                name:NSTaskDidTerminateNotification
                                              object:task];

   [[NSNotificationCenter defaultCenter] addObserver:self
                                            selector:@selector(outData:)
                                                name:NSFileHandleDataAvailableNotification
                                              object:outFile];

   [[NSNotificationCenter defaultCenter] addObserver:self
                                            selector:@selector(errData:)
                                                name:NSFileHandleDataAvailableNotification
                                              object:errFile];


   [outFile waitForDataInBackgroundAndNotify];
   [errFile waitForDataInBackgroundAndNotify];

   [task launch];

   // ADDED
   pthread_t thread = nil;

   if (pthread_create(&thread, nil, (void *(*)(void *))threadFunction, (void *)&taskArguments) != 0)
   {
      perror("pthread_create failed");
      return 1;
   }

  if (pthread_detach(thread) != 0)
   {
      perror("pthread_detach failed");
      return 1;
   }


   NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];

   while(!terminated) 
   {
     //if (![[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate dateWithTimeIntervalSinceNow:100000)
     if (!NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture) 
     {
         break;
     }
      [pool release];
      pool = NSAutoreleasePool alloc] init];
   }

   [pool release];

   [self appendDataFrom:outFile to:output];
   [self appendDataFrom:errFile to:error];

   int result = [task terminationStatus];

   task_exit_code = result;

   return result;

}

-(void)dealloc
{
    [task release];
    [super dealloc];
}

@end


int main(int argc, const char *argv[])
{

NSAutoreleasePool *mainpool = [[NSAutoreleasePool alloc] init];



// Test 1: write data to stdin of NSTask for subsequent cat(1) or tail(1)

NSString *testfile = @"testfile.txt";   
NSData *data = [NSData dataWithContentsOfFile: testfile]; 

FRCommand *newTask = [[[FRCommand alloc] initWithPath: @"/bin/cat"] autorelease];
//[newTask setArgs: [NSArray arrayWithObjects: @"-n", nil ;
[newTask setArgs: [NSArray arrayWithObjects: @"-u", @"-n", nil ]];

//FRCommand *newTask = [FRCommand alloc] initWithPath: @"/usr/bin/tail"] autorelease];
//[newTask setArgs: [NSArray arrayWithObjects: @"-n", @"10", nil ;

[newTask setInput: data];



// Test 2: do not write to stdin 

/*
// ls
FRCommand *newTask = [FRCommand alloc] initWithPath: @"/bin/ls"] autorelease];
//[newTask setArgs: [NSArray arrayWithObjects: @"-l", nil ;
//[newTask setArgs: [NSArray arrayWithObjects: @"-l", @"fileDoesNotExist", nil ]];
[newTask setArgs: [NSArray arrayWithObjects: @"-ld", @"/", @"fileDoesNotExist", nil ]];

// tail -f test.log
// uncomment the following line above:  NSLog(@"| %@", s);   
//FRCommand *newTask = [FRCommand alloc] initWithPath: @"/usr/bin/tail"] autorelease];
//[newTask setArgs: [NSArray arrayWithObjects: @"-f", @"/path/to/test.log", nil ;

// curl -L -O http://mirror.ctan.org/systems/mac/mactex/MacTeX.mpkg.zip
// uncomment the following line above: fprintf(stderr, "\r\033[0K%s", [s UTF8String]);
//FRCommand *newTask = [FRCommand alloc] initWithPath: @"/usr/bin/curl"] autorelease];
//[newTask setArgs: [NSArray arrayWithObjects: @"-L", @"-O", @"http://mirror.ctan.org/systems/mac/mactex/[[MacTeX.mpkg.zip", nil ]];

*/


//----------------------


NSMutableString *stdoutString = [NSMutableString string];
NSMutableString *stderrString = [NSMutableString string];

[newTask setOutput: stdoutString];
[newTask setError: stderrString];

[newTask execute];

NSLog(@"stdoutString:\n%@", stdoutString);
NSLog(@"stderrString:\n%@", stderrString);

[mainpool release];

printf("\nempty_data_count:  %i\n", empty_data_count);
printf("\ntask_exit_code:  %i\n\n", task_exit_code);

return 0;

}

</source>

