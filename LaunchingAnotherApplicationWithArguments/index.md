---
layout: page
title: LaunchingAnotherApplicationWithArguments
---

Here's the dilemma. I want to execute another application from within my app and pass it some arguments.

- Launch Services would be great, but I can't pass it any arguments other than file URL's.

- NSTask allows me to pass arguments. But the child proccess would have my process as parent process. Meaning that if my process exits, that child process woul also exit (and I don't want that!).

How can I fork another process, passing it some arguments, and be able to quit whitout that process dying?

-- Paulo Andrade

----

A child process launched by an NSTask will inherit the environment of its parent process. NSTask also has an instance method (**setEnvironment:**) to change the environment of the child process before it's launched (you have to set the environment before you launch the task). Use NSProcessInfo to get the environment's key/value pairs in the form of an NSDictionary. --zootbobbalu

----

Perhaps I didn't express my problem well. I need only do two things:
1 - Launch another application with a custom set of arguments. Example "$ /foo/Bar.app/Contents/MacOS/Bar arg1 arg2"
2 - Be able to launch the other application and exit mine whithout the other one exiting also.

Using NSWorkspace I can achiever number 2 but not number 1. That is, I can quit after launching but can't pass it any args.
Using NSTask I can achieve number 1 but nor number 2. That is, I can pass it args (via setArguments:) but can't quit after launching (if I do so the forked process wil exit).

Can I use NSTask to fork a process and then set it's parent process to be another process (like the init process)?

-- Paulo Andrade

----

AFAIK an NSTask will not terminat its associated child process when released, so I'm not sure why case number 2 is a problem. Have you tried to launch a child application with an NSTask and watched the child app terminate when the parent application quits? I have done exactly what you are trying to do without any problems many times. The only reason I suggested using environment variables is because it is more convenient. If your objective is to pass an array of values to a child process, then you should be able to use an NSTask object to do this. 

One thing to watch out for is the possibility that NSApplication may alter the arguments after NSApp is initialized. I couldn't find any documentation to explain this behavior, so why this has happened to me is a mystery. If I have to send arguments to a Cocoa application, I usually copy the arguments before NSApplicationMain().

    

int main(int argc, char *argv[])
{

	int result = 0;
	NSAutoreleasePool *innerPool = [[NSAutoreleasePool alloc] init];
	NSArray *arguments = [[NSProcessInfo processInfo] arguments];
	if ([arguments count])
		SomeGlobalExternVariable = [[NSArray alloc] initWithArray:arguments];

    result = NSApplicationMain(argc, (const char **) argv);


	[SomeGlobalExternVariable release];
	[innerPool release];

	return result;

}



  --zootbobbalu

----
OK, so maybe i've answered my own question. Reading intro(2) man page, I found this:

    
Parent process ID
             A new process is created by a currently active process; (see
             fork(2)).  The parent process ID of a process is initially the
             process ID of its creator.  If the creating process exits, the
             parent process ID of each child is set to the ID of a system
             process, init.


So if the parent exits while the child is still running, init will become the child's parent. Meaning that I can just use NSTask to fork the process and forget about it...

So, zootbobbalu, I believe you're right. Turns out number 2 isn't a problem!

-- Paulo Andrade

----
People are often confused because the behavior of a shell or Terminal window is to exit all child processes when the window is closed. However, this works because shells are a special case (I believe the terminology is "process group leader"), not because children are always taken down with their parent.

