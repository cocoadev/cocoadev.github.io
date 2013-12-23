---
layout: page
title: BCWrapperSource
---



Here's a wrapper for the UNIX bc command which is "a language that supports arbitrary precision numbers with interactive execution of statements." It can support huge numbers, and is built-in to OS X, and is much easier to figure out then using all the other classes.

See HandlingLargeNumbers, ConvertStringIntoExpression

Usage:
    
#import "BC.h"

...

NSString *operation = @"2*4";
NSString *result = [BC performMathOperation:operation];

NSLog(@"The result is: %@", result); // returns 8


    #import <Cocoa/Cocoa.h>

@interface BC : NSObject {}
+ (NSString *)performMathOperation:(NSString *)op;
@end

@implementation BC
+ (NSString *)performMathOperation:(NSString *)op
{
	if (op==nil || [op length]==0)
		return nil;
	
	NSTask *task = [[NSTask alloc] init];
	NSPipe *inPipe = [NSPipe pipe], *outPipe = [NSPipe pipe];
	NSFileHandle *inHandle = [inPipe fileHandleForWriting];
	NSFileHandle *outHandle = [outPipe fileHandleForReading];
	NSData *inData, *outData;
	NSString *outString;
	
	// the input must end with a new line character or you'll get syntax errors
	if ([op characterAtIndex:[op length]-1] != '\n')
		op = [op stringByAppendingString:@"\n"];
	inData = [op dataUsingEncoding:NSUTF8StringEncoding];
	
	[task setLaunchPath:@"/usr/bin/bc"];
	[task setStandardOutput:outPipe];
	[task setStandardError:outPipe];
	[task setStandardInput:inPipe];
	[task launch];
	
	[inHandle writeData:inData];
	[inHandle closeFile];
	
	outData = [outHandle readDataToEndOfFile];
	[task waitUntilExit];
	[task release];
	
	if (outData!=nil && [outData length])
	{
		outString = [[NSString alloc] initWithData:outData
			encoding:NSUTF8StringEncoding];
		if (outString)
			return [outString autorelease];
	}
	
	return nil;
}
@end

----

Shouldn't you make sure the mathlib is loaded? [ bc -l ]

----

How can using this not strike anyone as a horrible idea?

----
Don't worry, it does.

----
It's more of an example of how to use NSTask than being useful.

----

*"How can using this not strike anyone as a horrible idea?"*

Rather than simply firing toll-speak at someone's contribution, how about saying something useful such as the **way** in which it's a "horrible idea"? The concept is simple, really: instead of degrading this wiki by turning it into a repository of churlish self-superiority, add some actual wisdom.

----
Okay, then let's begin.  This wrapper class is an offshoot of a discussion of how to handle large numbers in Cocoa.  This "solution" is anything but because:
1) It involves creating an entirely separate process to do a simple calculation.  Compared to adding two large numbers, this is an inordinately expensive step, nevermind actually parsing the input and performing the same calculation your code could have done anyway.
2) It requires chewing up 2 file descriptors for the pipe.  This means syscalls, which means three expensive context switches into kernel mode, in order to redirect stdin, stdout, and stderr.
3) It requires the user to convert the entire mathematical operation into a string, meaning that it doesn't actually provide a mechanism for storing large numbers, just a way to perform mathematical operations on them.  Which means they ALREADY have to be stored somewhere, somehow.
4) It does no sanity checking on its input.
5) It stalls the calling process until the child process is completed.  This means potentially unresponsive UI.
6) It requires familiarity with bc, which is implemented in dc, which is a stack-based RPN calculator.
7) As pointed out above, it's potentially incorrect.

Does that satisfy you?  I do agree, however, that this is potentially useful as a quick demo of NSTask.

----

Yes, I'm satisfied - you've pointed out the technical reasons why this is a poor approach to the problem. That's far better than a vague insult.

----
I use a C library for large number support called MAPM ( http://www.tc.umn.edu/~ringx004/mapm-main.html )
I wrote a simple ObjC wrapper around the library, and I use it in a calculator that I have built for personal use.  It works pretty well.

