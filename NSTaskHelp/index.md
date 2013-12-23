---
layout: page
title: NSTaskHelp
---



**Can my NSTask inherit a tty from the launching environment?**

I have a simple cocoa app that calls terminal apps using NSTask.
This works fine and well until I build my deployment version,
one of the tasks does not work, the console outputs an error tcgetattr():operation is not supported.

Is it becuase of some missing environment argument?
Does debug work because the environment is gdb and not the default shell?

I am at a loss, it seems odd that the other tasks work always but this single one only works in debug builds.

----

What are you running in your task? tcgetattr() is used for manipulating the current terminal. When you use gdb, your program typically has a tty associated with it, just like when it's run from Terminal. When you don't use gdb and run from the Finder, you don't get a tty and apparently one of your tasks relies on the presence of one. You can check by running your app in Terminal and seeing if it works.

If that's the problem, you can fix it in two ways. One is to modify your tasks so that they don't require a tty. Most things you'd execute with NSTask shouldn't need one, so this might be possible, depending on what you want to do. The other way is to actually allocate a tty and hook it up to your task's standard IO. I've never done this, so I can't comment on how hard it might be. See     man 4 tty,     man 4 pty, and     man openpty for more info.

----
I believe this task requires a tty as its a remote debugger, I'll see if I can do away with it, pipe the output or as you suggested allocate a tty for the task.

(later....)

well pipes are not the answer, I am wondering if it can just be a setenvironment call that will allow the app to inherit a tty, nstask does not seem to have a lot of deep examples.

----

You can't inherit a tty because there is no tty to inherit; your app's parent is the window server, or loginwindow, or one of those things, and they don't have any attached tty. If you need one, you'll have to create your own. NSTask doesn't really need deep examples, because there isn't much deepness you can do with it. All of the farting around with ttys happens before you start to mess with NSTask.

----

I was under the impression the NSTask could inherit through a setenvironment call which I thought changes its environment. I guess there isn't really an environment key that can flag the task in this manner.  How does the os know the difference between a cli program and a gui ?

----

GUI programs are executed by the window server (or something similar to that). If you follow the chain of parent processes, you'll never get to a tty; the program's parent is the window server, whose parent is loginwindow, whose parent is... etc. That's why I say there's no tty to inherit. A CLI program is run inside Terminal, or an ssh or telnet session, and it has a tty because Terminal, sshd, or telnetd has created one.

NSTask is just a wrapper around fork() and exec(). It has nothing to do with ttys or anything like that, it's just an OO way to execute other programs.

----

**Unsolved problem in which app crashes - appears to include problems with setting up NSFileHandle**

I am trying to wrap a shell script that has the following usage:

Usage: magnetic [options] game [gfxfile]

It is an interpreter for a certain type of old text adventure games. The GUI of my application displays the output of the terminal and has a box where I can type in any commands. As I am extremely new to this kind of work I would like some advice on a few parts of my project.

First, when I attempt to load a game the application presents me with a spinning ball. No errors are given in the console; however the script works perfectly inside a terminal (I cannot see where the code I am using is at fault).

Also I am wondering how I could somehow place the script 'magnetic' inside the bundle. currently I launch it using the following code:

[magnetic setLaunchPath:@"/sw/bin/magnetic"];

which I guess will not work on any computer except my own. Is there quite a simple way to link the LaunchPath to a place within the bundle (for example if I was to place it inside 'magnetic/content/Resources/magnetic').

    
#import "MyController.h"

@implementation MyController

- (IBAction)createBanner:(id)sender
{
NSTask *magnetic;
NSPipe *output=[[NSPipe alloc] init];
NSString *theResult;

magnetic=[[NSTask alloc] init];
[magnetic setLaunchPath:@"/sw/bin/magnetic"];
[magnetic setArguments:[NSArray arrayWithObject:[bannerField stringValue]]];
[magnetic setStandardOutput:output];
[magnetic launch];
theResult=[[NSString alloc]
initWithData:output fileHandleForReading]
availableData]
encoding:[[NSASCIIStringEncoding];
[outputField setString:theResult];
[output release];
[magnetic release];
}

@end


Thanks, and sorry if I have made a lot of silly mistakes.

Johnny G

----

from Apple's docs

*
- (NSData *)availableData

Returns the data available through the receiver. If the receiver is a file, returns the data obtained by reading the file from the file pointer to the end of the file. If the receiver is a communications channel, reads up to a buffer of data and returns it; if no data is available, the method blocks. Returns an empty NSData if the end of file is reached. Raises NSFileHandleOperationException if attempts to determine file-handle type fail or if attempts to read from the file or channel fail.
*

so maybe no data is available. Maybe if you run in Debug you could see where it stalls. Sorry this is not a very good response to your question. But you really want to know where it stalls, and also could you tell if your task is supposed to terminate? Is it sending some output messages? Do you have a way to put more messages inside the script?

Also, it would probably be better to have non-synchronous handling of output and error. Because the way you do it now, theResult might miss some of the ouput, no?  

For the bundle, it is really easy:
    
NSString *thePath=[[NSBundle mainBundle] pathForResource:@"magnetic" ofType:@""];
magnetic=[[NSTask alloc] init];
[magnetic setLaunchPath:thePath];
//etc...

Then you can make your script a resource and add it to your project and to the application target. It will be automatically added inside the resources when you build. You should even be able to work on it inside XCode. You may even get the syntax coloring and everything (is it a shell script?). If you modify the script, it will be updated in the app bundle at the next build (so do not work on the version inside the built product).

----

*Editor's note:*

The original discussion took place long ago, and the OP is probably long gone. Looks like basic problems setting up the file handles.

----

