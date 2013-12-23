---
layout: page
title: NSLogToFile
---

How can I get NSLog to send its output to a different file instead of the console? Do I need to change the stderr pointer or is there someway to do this through the build settings or an environment variable? Also, do I need to do anything special for this in multiple threads?

Thanks.

--RyanBates

----

See the NSLog and NSLogv docs at [http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Functions/FoundationFunctions.html]

*NSLogv writes the log to STDERR_FILENO if the file descriptor is open. If that write attempt fails, the message is sent to the syslog subsystem, if it exists on a platform, with the LOG_USER facility (or default facility if LOG_USER does not exist), with priority LOG_ERR (or similar). If both of these attempts to write the message fail, the message is discarded.*

Depending on what you're logging, you could also use NSString writeToFile methods, i suppose.

----

    
#include <stdio.h>
.
.
.
freopen("/tmp/mystderr.log", "a", stderr);


- NeilVanNote

----
How would one use all this to say, put everything from the console in an NSTextView, that updates just like the console?

**what do you mean by that?  it's pretty ambiguous**

*You wouldn't use this at all, you'd use NSTask and NSPipe. There's a ton of tutorials out there on how to do it.*

To put stuff coming from your standard out into your text view, you'd could use pipe() to make two unix file descriptors.  Set one end of the pipe to be your standard out, and then read from the other side.  You can use the run loop to read from the pipe and notify you if you have some incoming information. BookCoreMacOSXandUnix has information on these topics.
----
For the quick and dirty on piping data between tasks:

http://developer.apple.com/documentation/Cocoa/Conceptual/OperatingSystem/Tasks/pipes.html

Note that this documentation states that *The launched process in this example must get data and write that data, using NSFileHandle�s writeData:, to its standard output device (obtained NSFileHandle�s fileHandleWithStandardOutput).*; which is not entirely accurate. Any program that reads/writes to the STDIN_FILENO, STDOUT_FILENO, or STDERR_FILENO file handles can be used.

As the previous poster indicated, NSFileHandle's background notifications can be employed so that long running processes don't hang your program. Actually, they should probably be used even for short running programs, just to be safe.

----

Here is how I have implemented redirecting all output from NSLog to my programs log file.  The code resides in main.m so that NSLog is correctly redirecting from the very start of the cocoa program.  This might help some people unfamiliar with C strings.

    
#import <Cocoa/Cocoa.h>
#include <stdio.h>

int main(int argc, const char *argv[])
{
	char *val_buf, path_buf[155];
	
	val_buf = getenv("HOME");
	sprintf(path_buf,"%s/Library/Logs/Meteo.log",val_buf);
	freopen(path_buf,"a",stderr);
    
	return NSApplicationMain(argc, argv);
}


-Joe
----

I would suggest replacing the 155 constant length with **MAXPATHLEN** or the result of a **sysctl** *(man 3 sysctl)* call to grab how long paths can be in conjunction with a **malloc** call. As is, this is a prime candidate for a buffer overflow.

**MAXPATHLEN** is defined in **<sys/param.h>**. The vendor (Apple) of course is free to change such things between revisions thus the **sysctl** method is safer for executables that can run on multiple versions of the OS as **MAXPATHLEN** is just reduced to a numeric constant at compile time.

Cheers
----
Even better, avoid potentially-overflowing C arrays and use Cocoa goodness:
    
int main(int argc, const char *argv[])
{
	id pool = [NSAutoreleasePool new];

	NSString *logPath = [NSHomeDirectory() stringByAppendingPathComponent:@"Library/Logs/Meteo.log"];
	freopen([logPath fileSystemRepresentation], "a", stderr);

	[pool release];
    
	return NSApplicationMain(argc, argv);
}


----

A very good.  I was (for some reason) under the impression that you could not use objective c and cocoa calls from within main().  But that code works and is much easier than C arrays.  Thanks!

----

ObjC is nothing magical. A line like [obj foo] is translated directly into a function call to objc_msgSend(). The only time you can't use ObjC in a function is if the function is in a .c file, or if there are runtime issues (like using ObjC in a signal handler).

----

I will play devils advocate here.

That created a NSAutoreleasePool (and anything it relies on), just for building the path (and anything it relies on), and then destroyed it (huge expense). The POSIX method would run within 1000th of the time. Not to mention that a pure, properly coded POSIX sysctl would have caught the over-length problem straight out. As coded in the Foundation (ObjC) example, I am not so sure. It would depend on if the path was truncated, and the result matched an existing file.

Such a waste.
----
I doubt if it's that much slower. The primary overhead in this case will be the call to freopen(), which will involve two or more system calls. But even if it's not, who cares if the ObjC version is a thousand times slower? This is code that will execute exactly one time, and it will not take a significant amount of time regardless. The ObjC code is, in my opinion, cleaner, easier to read, and has fewer chances for bugs.

*As coded in the Foundation (ObjC) example, I am not so sure. It would depend on if the path was truncated, and the result matched an existing file.*

I'm afraid I have no idea what you're trying to say here. Can you explain?

----

If you wish to trash all the NSLog messages you can also redirect to "/dev/null" using the above technique.

----

You could use snprintf to avoid overflow.

----

I wanted to be able to redirect NSLog to a file, but still be able to view the log within a NSTextView - very similar to the way Disk Utility works. I couldn't find an example of this so I created the code myself. I am new to Cocoa, so correct any of my mistakes. This code is working fine in my program. I hope this helps.

    
-(void)awakeFromNib
{
	NSString *logPath = [NSHomeDirectory() stringByAppendingPathComponent:@"Library/Logs/YOUR_LOG_FILE.log"];
	
	NSFileHandle *fh = [NSFileHandle fileHandleForReadingAtPath:logPath];

	//Read the existing logs, I opted not to do this.
	//[logWindowTextField readRTFDFromFile:logPath];
	
	//Seek to end of file so that logs from previous application launch are not visible
	[fh seekToEndOfFile];

	[[NSNotificationCenter defaultCenter] addObserver:self
			selector:@selector(getData:)
			name:@"NSFileHandleReadCompletionNotification"
			object:fh];
	[fh readInBackgroundAndNotify];
}
- (void) getData: (NSNotification *)aNotification
{
    NSData *data = aNotification userInfo] objectForKey:[[NSFileHandleNotificationDataItem];
    // If the length of the data is zero, then the task is basically over - there is nothing
    // more to get from the handle so we may as well shut down.
    if ([data length])
    {
        // Send the data on to the controller; we can't just use +stringWithUTF8String: here
        // because -[data bytes] is not necessarily a properly terminated string.
        // -initWithData:encoding: on the other hand checks -[data length]
        NSString *aString = [[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] autorelease];
		
		[logWindowTextField setEditable:TRUE];
		[logWindowTextField insertText:aString];
		[logWindowTextField setEditable:FALSE];

		//Scroll to the last line
		[self setScrollToVisible];

		// we need to schedule the file handle go read more data in the background again.
		aNotification object] readInBackgroundAndNotify];
    }
	else
	{
		//I use a delay to minimize CPU usage when the file has not changed.
		[self performSelector:@selector(refreshLog:) withObject:aNotification afterDelay:1.0];
	}
}
- (void) refreshLog: ([[NSNotification *)aNotification
{
	aNotification object] readInBackgroundAndNotify];
}
-(void)setScrollToVisible;
{
	[[NSRange txtOutputRange;
	
	txtOutputRange.location = [[logWindowTextField textStorage] length];
	txtOutputRange.length = 0;
	[logWindowTextField setEditable:YES];			
	[logWindowTextField scrollRangeToVisible:txtOutputRange];
	[logWindowTextField setSelectedRange:txtOutputRange];
	[logWindowTextField setEditable:NO]; 
}


----

Dear devil's advocate, please read Mike Ash's "Autorelease Is Fast" before you go casting aspersions.

http://www.mikeash.com/pyblog/autorelease-is-fast.html

----

