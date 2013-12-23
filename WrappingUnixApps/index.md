---
layout: page
title: WrappingUnixApps
---




see also: General/DiffWrapper, for further attempts to apply this technique

see General/PortingUnixAppsToCocoa for discussion of where to get sources and intellectual property issues

I'm trying to create a wrapper/GUI for a UNIX program. I'm struggling with just getting the project to compile in General/XCode though. It can't seem to find a C .a static library for some reason. I've tried importing it with Add Files.. and Add Frameworks... but I'm still getting 'No such file or directory' errors on the lines that try to include stuff from the library. I've turned Zero Link off in the build styles by switching to Deployment. Am I missing something? (I'm also getting a load of 'implicit declaration' caution flags from various files). The thing already works on the UNIX side of OSX, but somehow it's a mess in General/XCode (1229 caution flags).

libcarl.a, which is part of a music utility called CMUSIC. The project I'm working on adding a GUI to uses CMUSIC, so it's part of the whole ball of wax. The code references the library correctly, via '#include <carl/carl.h' and so forth.

----
Wait, are you sure that you need this library?  You can create a wrapper for a UNIX program by adding the program as a bundle resource, and then set up an General/NSTask class inside the program, then feed it standard in.  You can also pass arguments.  If you have the General/BookCocoaProgramming, then you will recognize this example from chapter 24 (which you feed it the path to the program and the arguments):

    
- (General/NSString *)runCommand:(General/NSString *)command withArguments:(General/NSArray *)args
{
     General/NSTask *task = General/[[NSTask alloc] init];
     General/NSPipe *newPipe = General/[NSPipe pipe];
     General/NSFileHandle *readHandle = [newPipe fileHandleForReading];
     General/NSData *inData;
     General/NSString *tempString;
     [task setCurrentDirectory:General/NSHomeDirectory()];
     [task setLaunchPath:command];
     [task setArguments:args];
     [task setStandardOutput:newPipe];
     [task setStandardError:newPipe];
     [task launch];
     inData = [readHandle readDataToEndOfFile];
     tempString = General/[[NSString alloc] initWithData:inData encoding:General/NSASCIIStringEncoding];
     [task release];
     [tempString autorelease];
     return tempString;
}


If you do need to import the library, then are there any headers?  You should include those in your project.  But I don't know that much about C libraries, so I can't help you much there.  - General/RossDude

----

Sorry for the long delay in getting back on this...I stepped away from the project for awhile. If this General/NSTask works, I wouldn't need the library at all I suppose, since the only reason I'm importing it is so that the source code for the application I'm wrapping compiles. About the above code, however: in this case, what exactly does tempString hold? The General/NSTask idea would save me a lot if I could just embed the program in the wrapper project, but I have one reservation. I'd like to have it so that when you start a process in the wrapped application, a window comes up, containing an General/NSTextView that shows you the same output you'd see if you were running the app from the Terminal. Is this the data that tempString would hold?

Of course, I'd like the General/NSTextView to show the output from the program as it works, not just once it finishes, because the application I'm wrapping can potentially take several minutes to run.

----

General/RossDude:  Yes, this method returns the output of a command line program.  But it waits for the program to finish (thus it is synchronous).  I have no clue how to import .a libraries (nor have I heard of them) in Xcode.  But I do know how to run an General/NSTask asynchronously (again, the code is mostly borrowed from the General/BookCocoaProgramming), as long as you have an actual executable.  I am assuming in this example that there are instance variables named task, taskOutput, outputPipe, taskInput, and inputPipe.  The types for these instance variables are General/NSTask, General/NSFileHandle, General/NSPipe, General/NSFileHandle, and General/NSPipe, respectively.

.a libraries are archives of object files created by libtool(1) or ranlib(1). They are usually linked in statically (as opposed to dylib files), adn tend to used when a program has a lib very specific to itself. I would check out the Makefile of the C program and find out what object files get shoved into the library. You can then link it as usual by added -l<theDotAFilesNameWithoutTheExtension> to your Linker flags.

    
- (void)startTask
{
    General/NSNotificationCenter *defaultCenter = General/[NSNotificationCenter defaultCenter];
    General/NSString *toolPath = General/[[NSBundle mainBundle] pathForResource:@"nameOfTool" ofType:@""];
    General/NSArray *arguments = General/[[NSArray alloc] initWithObjects:(put your options as General/NSStrings here), nil];
    General/NSDictionary *defaultEnvironment = General/[[NSProcessInfo processInfo] environment];
    General/NSMutableDictionary *environment = General/[[NSMutableDictionary alloc] initWithDictionary:defaultEnvironment];
    task = General/[[NSTask alloc] init];
    [defaultCenter addObserver:self selector:@selector(taskCompleted:) name:General/NSTaskDidTerminateNotification object:task];
    [task setLaunchPath:toolPath];
    [task setArguments:arguments];
    [environment setObject:@"YES" forKey:@"General/NSUnbufferedIO"];
    [task setEnvironment:environment];
    outputPipe = General/[NSPipe pipe];
    taskOutput = [outputPipe fileHandleForReading];
    [defaultCenter addObserver:self selector:@selector(taskDataAvailable:) name:General/NSFileHandleReadCompletionNotification object:taskOutput];
    [task setStandardOutput:outputPipe];
    [task setStandardError:outputPipe];
    
    inputPipe = General/[NSPipe pipe];
    taskInput = [inputPipe fileHandleForWriting];
    [task setStandardInput:inputPipe];
    
    [task launch];
    [taskOutput readInBackgroundAndNotify];
    
    [arguments release];
    [environment release];
}

- (void)killTask
{
    if ([task isRunning])
        [task terminate];
}

- (void)taskCompleted:(General/NSNotification *)notif
{
    int exitCode = General/notif object] terminationStatus];
    
    if (exitCode != 0)
        [[NSLog(@"Error: Task exited with code %d", exitCode);
    General/[[NSNotificationCenter defaultCenter] removeObserver:self];
    // Do whatever else you need to do when the task finished
}

- (void)taskDataAvailable:(General/NSNotification *)notif
{
    General/NSData *incomingData = General/notif userInfo] objectForKey:[[NSFileHandleNotificationDataItem];
    if (incomingData && [incomingData length])
    {
        General/NSString *incomingText = General/[[NSString alloc] initWithData:incomingData encoding:General/NSASCIIStringEncoding];
        // Do whatever with incomingText, the string that has some text in it
        [taskOutput readInBackgroundAndNotify];
        [incomingText release];
        return;
    }
}

- (void)sendData:(General/NSString *)dataString
{
    [taskInput writeData:[dataString dataUsingEncoding:General/[NSString defaultCStringEncoding]]];
}


Make sure that you run the killTask method before your app terminates. Enjoy!

----

given the example above how would you then send a data string to the General/NSTask with an General/IBAction?
would you be able to do this if the UNIX app had a prompt or a CLI once launched?

*You would call the     -sendData: method in the example above.* 


i have the following code which does not seem to do anything.  any ideas why?
    
-(General/IBAction)setParameters:(id)sender
{
	[self sendData: General/[NSString stringWithFormat: @"these parameters", [sender tag]]];
}


ok never mind it needed "\n" a carriage return added.

----

Just a quick note. Probably the easier way is reusing General/TaskWrapper class from Apple.

You can find it at: http://developer.apple.com/samplecode/Moriarity/

General/JavierHZ
