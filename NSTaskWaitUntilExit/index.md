---
layout: page
title: NSTaskWaitUntilExit
---

I've got an NSTask which is doing some simple Perl regex on an string. I thought I should call     waitUntilExit to make sure it was completely done before using the output of the task, but that method is causing the app to "freeze" (sorta). It's getting called from a framework and it takes over the UI so I just have to force quit. Any one know why this is happening? The Perl is just a few lines that read in all <STDIN> and then do a regex replace on it. If I remove     waitUntilExit it works fine. But I thought I had to have it... --KevinWojniak

Found the problem. When you use     waitUntilExit you have to read in the data before that is called or else the pipe gets filled up (see http://cocoa.mamasam.com/COCOADEV/2003/10/1/73890.php ). So I had this:
    
[task launch];
[inHandle writeData:writeData];
[inHandle closeFile];
[task waitUntilExit];
readData = [outHandle readDataToEndOfFile];
[task release];

But needed this:
    
[task launch];
[inHandle writeData:writeData];
[inHandle closeFile];
readData = [outHandle readDataToEndOfFile];
[task waitUntilExit];
[task release];


----
I'd like to suggest to use NSFileHandle's readInBackgroundAndNotify and NSTaskDidTerminateNotification
in order to prevent the UI freeze.

----

*This is not an issue if your task will terminate promptly, which could be the case for a lot of UNIX commands, but it's a good suggestion for longer-running commands.*

----

I agree, I originally imagined commands like TeX or ImageMagick.

----

**Be careful when using NSTaskWaitUntilExit**

*Editor's Note: Moved from T**'erminateNSTaskWithWaitUntilExit*

I launch an NSTask as follows:

    
    NSTask *curlTask = [[NSTask alloc] init];
    NSPipe *curlPipe = [[NSPipe alloc] init];
    NSString *curlHTML;
    
    [curlTask setLaunchPath:@"/sw/bin/curl"];
    [curlTask setArguments:[NSArray arrayWithObject:@"http://www.mywebsite.com"]];
    [curlTask setStandardOutput:curlPipe];
    [curlTask launch];
    [curlTask waitUntilExit];


Funny thing is the thread hangs, but  I can plainly see curl being done -- stdErr says 100% xferred, and curl isn't doing anything, and if I use the exact same command via the command line, curl exits.  What gives?  BTW, the exact same thing happens if poll for *[curlTask isRunning]*.  How come it won't terminate?  Help!!!!

----

    /usr/bin/curl works so it could be fink's version of curl that is causing the problem. You might have to read the pipe first (with an NSFileHandle object) and then close the handle before the task will exit.

    
    NSTask *curlTask = [[NSTask alloc] init];
    NSPipe *curlPipe = [[NSPipe alloc] init];
    NSFileHandle *outputHandle = [curlPipe fileHandleForReading];
    NSString *curlHTML;
    [curlTask setLaunchPath:@"/sw/bin/curl"];
    [curlTask setArguments:[NSArray arrayWithObject:@"http://www.mywebsite.com"]];
    [curlTask setStandardOutput:curlPipe];
    [curlTask launch];
    NSData *outputData = [outputHandle availableData];
    [outputHandle closeFile];
    [curlTask waitUntilExit];



as a side note, NSFileHandle can read in background and notify you of available data to allow your app to do other things (    readInBackgroundAndNotify). 

----

If you're having trouble getting your NSTask to exit (quitting the app), have a read of TerminateOrStopGood.

----

Note that you should *never* do a     waitUntilExit without first emptying out the stdout pipe unless you know that your task will not produce any output whatsoever. Pipes have a limited and small buffer size, and if the subtask hits the limit and your application doesn't empty it, it will hang waiting for the pipe to be emptied. If you're in turn waiting for it to exit, you'll deadlock and never get anywhere.

----

We've observed a situation which can cause     waitUntilExit to hang that's a little different than above.  If the thread which called     launch no longer exists when the NSTask ends (for whatever reason), then the NSTaskDidTerminateNotification can't be posted, which causes bad behavior from     waitUntilExit.  On Tiger we've seen it actually generate a signal and cause the application to 'vanish', whereas on Leopard it seems to just cause a deadlock.

