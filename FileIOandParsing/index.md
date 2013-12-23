---
layout: page
title: FileIOandParsing
---



Various concepts and techniques for reading from, writing to and parsing text files.

----
I was doing some work the other day for my CS class appending text to a single file several times. I was looking for a simple Cocoa method that could just add a string to the end of an existing file, but I couldn't find a simple one anywhere! The all included lots of calls involving NSFileHandle, etc. I wanted something as simpe as NSString's -writeToFile: method, so I wrote a catagory for NSString called -appendToFile:, as shown below:

    
#import "NSString+KPExtensions.h"
#import <fcntl.h>
#import <sys/stat.h>

@implementation NSString (KPExtension)
- (void)appendToFile:(NSString *)path
{
    int fd = open([path cString],
                  (O_WRONLY | O_CREAT | O_APPEND),
                  (S_IRWXU | S_IRWXG | S_IRWXO));
    NSFileHandle *file = [[NSFileHandle alloc] initWithFileDescriptor:fd];
    [file writeData:[self dataUsingEncoding:NSASCIIStringEncoding]];
    [file closeFile];
    [file release];
}
@end


As you can see, I'm simply creating a Unix file descriptor for the file with all the desired flags to make it just write to the end of the file, wrapping it in an NSFileHandle, and then calling the -writeData: method to write it to the end of the file.

OK, I'm sort of just a beginner at Cocoa, so this was the solution I was able to come up with. I'm sure there's a better way, and that there's millions of things wrong with it, so feel free to critique.

- KevinPerry

Or better, fix, with an explanation.

**How about an atomic version (that'll get KritTer's attention) so you won't corrupt path, ever? -- RobRix**

*I'm beginning to feel maligned :) -- KritTer*

**You know we love you ;) -- RobRix**

    Don't blame me, I don't post anonymously -- MikeTrent

**I've taken to it lately. Yes, yes, it was me. Signed retroactively. -- RobRix**

