---
layout: page
title: NSTask
---

*
Using NSTask, your program can run another program as a **subprocess** and can monitor that program's execution. NSTask creates a separate executable entity; unlike NSThread, it does not share memory space with the parent process.

By default, a task inherits several characteristics of its parent's environment: the current directory, standard input, standard output, standard error, and the values of any environment variables. If you want to change any of these, e.g., the current directory, you must set a new value before you launch the task. **A task's environment is established once it has launched.**

**An NSTask can only be run once. Subsequent attempts to run an NSTask raise an error.**
*

If you run a task from a document in a document-based application, you should (at the very least) send the     terminate message to the task instance in the cleanup code for the document. See also NSTaskTermination for more discussion.

Other pages at CocoaDev related to using NSTask (and see particularly NSPipe and NSFileHandle):

**TODO:** since the migration to MediaWiki this embedded search is broken. I guess we could use http://www.mediawiki.org/wiki/Extension:DynamicPageList_(Wikimedia) and put all the relevant pages into an NSTask category, but that seems like redundant work. Perhaps someone knows of a way to do a similar embedded search results in MediaWiki but I couldn't find one. -- markaufflick

----
**Example: Launching an NSTask** (this is intended only as a simple example of launching a task, using the UNIX     ls command, and passing arguments to it. Of course, if you just want to get a directory' contents, you should use NSFileManager. The usual way to redirect the standard input and output of the task is to use NSPipe and NSFileHandle, as shown below:

<syntaxhighlight lang="objc">
 //Launch "ls -l -a -t" in the current directory, and then read the result into an NSString:
 
 NSTask *task = [NSTask new];
 [task setLaunchPath:@"/bin/ls"];
 [task setArguments:[NSArray arrayWithObjects:@"-l", @"-a", @"-t", nil]];
 
 NSPipe *pipe = [NSPipe pipe];
 [task setStandardOutput:pipe];
 
 [task launch];
 
 NSData *data = pipe fileHandleForReading] readDataToEndOfFile];
 
 [task waitUntilExit];
 [task release];
 
 NSString *string = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
 NSLog (@"got\n%@", string);
 [string release];
</syntaxhighlight>

keywords: [[NSTask, NSFileHandle, NSPipe

----

For more, see the CocoaDev pages on NSPipe and NSFileHandle, and:

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSTask_Class/index.html

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSPipe_Class/index.html

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSFileHandle_Class/index.html

Tutorials that set up more sophisticated communication with the running task from within a Cocoa GUI interface include:

Apple: Moriarity - http://developer.apple.com/samplecode/Moriarity/index.html

Cocoadevcentral Tutorial: http://cocoadevcentral.com/articles/000025.php

BookBuildingCocoaApplications presents an extended example developing GUI that communicates with an auxiliary task, as, I believe, does BookCoreMacOSXandUnix.

----

An NSTask will break Xcode's debug log entirely if you execute ANYTHING related with sh or bash (including scripts). printf, NSLog; all will cease to function as soon as the task has been launched. Even things like right clicking on an object in the debugger will yield nothing (straight GDB still prints though). The best way around this bug is to use a different shell, like zsh (which should be compatible with all your sh/bash commands).

----

Actually, I looked back in on the error again, after finding out that zsh was not the solution to my problem (my scripts still weren't working). I figured out that the problem lies with standard input, of all things. A quick fix for this would be to set your standard input up to something random, like a pipe, and do nothing with it.

<syntaxhighlight lang="objc">
 NSLog(@"Starting log...");
 NSTask *task = [NSTask alloc]init]autorelease];
 NSPipe *pipe = [NSPipe pipe];
 
 [task setLaunchPath:@"/bin/sh"];
 [task setArguments:args];
 [task setEnvironment:env];
 [task setStandardOutput:pipe];
 //The magic line that keeps your log where it belongs
 [task setStandardInput:[NSPipe pipe;
 NSLog(@"Log is setup here. Preparing to launch...");
 ...
 [task launch];
 NSLog(@"I usually am not in Xcode's console, but I am now!");
</syntaxhighlight>

----

NSTask-related sample code (added Oct. 2010):

1.  OpenFileKiller    --    http://projectswithlove.com/projects/index.html

A Mac project that shows how to invoke other processes - optionally with root privileges - and get the results in a single line. 
The sample application can kill the processes that have opened a specific file.


2. PseudoTTY.app    --    http://amath.colorado.edu/pub/mac/programs/

similar in structure to Apple's Moriarity example, except for using pseudo-ttys instead of pipes for (mostly) line-buffered inter-process streams.
(see man 4 pty and man 4 tty)


3. asynctask.m    --    http://www.cocoadev.com/index.pl?NSPipe

a command-line tool that shows how to implement asynchronous stdin, stdout & stderr streams for processing data with NSTask;
creates its own NSRunLoop; uses asynchronous "waitForDataInBackgroundAndNotify" notifications and pthread_create(3) and 
pthread_detach(3) for writing more than 64 KB to the stdin of an NSTask.


4. Task.{h,m}    --    http://code.google.com/p/polkit/

NSTask wrapper (for Mac OS X 10.6)


5. MYTask.{h,m}    --    https://bitbucket.org/snej/myutilities/src

NSTask wrapper (uses garbage collection)


6. AMShellWrapper    --    http://www.harmless.de/cocoa-code.php

Based on TaskWrapper from Apple's Moriarity sample code, use this class to run commandline tools from your application. 
Connect your own methods to stdout and stderr, get notified on process termination and more.


7. CocoaScript.zip    --    http://cocoawithlove.com/2010/11/cocoa-application-for-running-scripts.html

a Cocoa application for running scripts


8. MFTask    --    https://bitbucket.org/iracooke/mftask/src

a wrapper around NSTask that makes some things like monitoring output easier

