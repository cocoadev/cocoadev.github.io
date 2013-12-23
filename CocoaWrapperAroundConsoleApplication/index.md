---
layout: page
title: CocoaWrapperAroundConsoleApplication
---



see PortingUnixAppsToCocoa for information about where to get sources (or, for that matter, pre-built binaries) to wrap, and discussion of intellectual property issues

How can you run a terminal script behind a Cocoa interface? I have a UNIX script precompiled that asks the user for input during its run. How could I interact with it in, e.g., an NSTextView?

----

The best example is the Moriarity sample code from Apple (see just below).

Understanding the Apple documentation on the classes *NSTask* and *NSPipe* is essential to progressing with this type of project.

There are also such applications as Pashua or Platypus that are intended to help with this, if the interaction is fairly simple.

----

**Building AQUA Interfaces for Terminal Applications**

Using a combination of the Foundation classes NSTask and NSPipe, we can start a task and communicate with it using stdin and stdout.
The main NSTask page presents a simple example that uses the UNIX     ls command, with arguments.

There are several tutorial examples of how to do this with a more complicated interface:

Apple: Moriarity - http://developer.apple.com/samplecode/Sample_Code/Cocoa/Moriarity.htm

Cocoadevcentral Tutorial: http://cocoadevcentral.com/articles/000025.php

----

When working with Foundation tools, NSFileHandle provides a handy means of interacting with stdin, stdout, and stderr.

Since     availableData (and other) methods of  NSFileHandle return an NSData, you can extract that data into an NSString by calling:

    
[data getBytes:buffer];
someString* = [NSString stringWithUTF8String:buffer];


Where data is your data object, and buffer is a char array (char buffer[20]).

-- RobRix

----

What about sending stdin (StandardInput) to a terminal app?

Command-line arguments are easy, but I haven't seen any sample code for sending stdin... 

----

I tried it recently, but it seemed like the terminal app never got the end-of-file I sent using closeFile: on the fileHandleForWriting: . Any TipsAndTricks or SampleCode for this?

-- MichaelMcCracken

----

**Setting up ongoing communication**

I am trying to write a cocoa interface to a console app.  Now there are a lot of examples showing how to wrap a one-off tool such as 'ls' with NSTask.  Basically the task waits until the result is ready and returns that.  Great.  What about when the task you are wrapping is an ongoing one, such as a server, or a perl application?  Then NSTask essentially hangs your cocoa app waiting for the terminal task to finish, which it never does since it isnt meant to without an explicit quit command.  

So how to get the data from NSTask while the wrapped app is still running?  After some more sleuthing in examples I realized I need to use a filehandle that readsInBackgroundAndNotifies so that control is returned to the main loop.  That much worked fine.  However, any attempts to read from the filehandle with [myFilehandle readDataToEndOfFile] followed by another [myFilehandle readInBackgroundAndNotify] in the notification's selector method didn't work as expected.  I was hoping that this would read the data and clear the filehandle to wait for more data.  In the end I am using the returned data from the notification: notification userInfo]objectForKey:[[NSFileHandleNotificationDataItem]; This works fine but I am worried that I am introducing unnecessary latency or inefficiency by not reading from the filehandle directly.  Can anyone comment on this?

EcumeDesJours

----

I think the best way would be to use a separate thread for the communication between your application and your tool.

-- EricForget

----

It sounds like the problem is that you are trying to     readDataToEndOfFile which will wait for the communications pipe to close.  If you just use the     availableData you may do better.

----

**Further applicability** (though this Q-and-A appears redundant in relation to the above)

I'm messing around with an app that'll let me control some basic functions on my mac using a PDA, over http.

How can I make my app run an external executable? I have this piece of code:

    
if ([response isEqualToString:[[NSString alloc] initWithString:@"1"]]) {
	[statusBox setStringValue:statusBox stringValue] stringByAppendingString:@"\nThe server has indicated that a connection should be established.\nLaunching executable...";
	//The executable should be launched here
	[statusBox setStringValue:statusBox stringValue] stringByAppendingString:@"\nResetting connection indicator...";
	NSURL *URL2 = [NSURL URLWithString:@"http://www.exdev.dk/connector/reset.php"];
	NSData *data2 = [URL2 resourceDataUsingCache:NO];
	NSString *response2 = [[NSString alloc] initWithBytes:[data bytes] length:[data length] encoding:NSASCIIStringEncoding];
} else {
	[statusBox setStringValue:statusBox stringValue] stringByAppendingString:@"\nThe server reported no awaiting connections.";
	}


----
If it's a command-line tool, check out NSTask. Otherwise, NSWorkspace might be the way to go. --JediKnil

----

Quick example of running a task (ls -l) synchronously (this will block on long tasks, probalby not good to run in the main thread) and getting the
stderr and stdout into a single NSString:

    

    NSTask *task = [NSTask new];
    [task setLaunchPath:@"/bin/ls"];
    [task setArguments:[NSArray arrayWithObject:@"-l"]];
    [task setStandardOutput:[NSPipe pipe]];
    [task setStandardError:[task standardOutput]];
    [task launch];
    NSData* output = [task standardOutput] fileHandleForReading] readDataToEndOfFile];
    [[NSString* out_string = [[[NSString alloc] initWithData:output encoding:NSUTF8StringEncoding] autorelease];
    NSLog(out_string);



----

> How can you run a terminal script behind a Cocoa interface? I have a UNIX script precompiled that asks the user for input during its run.  How could I interact with it in, e.g., an NSTextView?

Have a look at the source code of PseudoTTY.app.

http://amath.colorado.edu/pub/mac/programs/

In PseudoTTY/PtyView.m replace

NSString * cmd = @"/bin/sh";

with your interactive command line app

NSString * cmd = @"/path/to/interactive/cliapp";

