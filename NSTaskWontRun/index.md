---
layout: page
title: NSTaskWontRun
---



I've got an app wrapping a UNIX application. I've used NSTask before (in fact, I'm just coming off of writing another wrapper, so I've essentially cut and pasted my NSTask code).In this new app though, I'm giving the [task launch] command and getting no results. I've included the two methods I think are relevant: one sets up the NSTask, the other starts it:

    

-(void)initializeTask:(NSString *)resourcePath
{
////Set the path
	NSString * toolPath;
	printf("Initialize task %s\n", [resourcePath UTF8String]);
	toolPath = [[NSBundle mainBundle] pathForResource:resourcePath ofType:@""];
	
////Set the environment
	NSDictionary *defaultEnvironment = [[NSProcessInfo processInfo] environment];
	NSMutableDictionary *environment = [[NSMutableDictionary alloc] initWithDictionary:defaultEnvironment];
	
	task = [[NSTask alloc] init];
	
	[task setLaunchPath:toolPath];
	
	[environment setObject:@"YES" forKey:@"NSUnbufferedIO"];
	[task setEnvironment:environment];
	
////Set the pipes
	outputPipe = [NSPipe pipe];
	taskOutput = [outputPipe fileHandleForReading];
	[defaultCenter addObserver:self selector:@selector(taskDataAvailable:) name:NSFileHandleReadCompletionNotification object:taskOutput];
	[task setStandardOutput:outputPipe];
	[task setStandardError:outputPipe];
	
	
	inputPipe = [NSPipe pipe];
	taskInput = [inputPipe fileHandleForWriting];
	[task setStandardInput:inputPipe];
	
	[taskOutput readInBackgroundAndNotify];
	
	[environment release];
	
	printf("Initialized\n");
	
}



-(IBAction)runFoo:(id)sender
{
	[self initializeTask:@"Foo"];
	NSString * flagTemp = [[NSString alloc] initWithString:@""];

	arguments = [[NSMutableArray alloc] init];
	

////Parsing Arguments from GUI
	int i;

	for(i = 0; i < [flags count]; i++) {
		[flagTemp release];
		flagTemp = flags objectAtIndex:i] retain];
		if(i < 2)
			[arguments addObject:[flagTemp stringByAppendingString:[[outlets objectAtIndex:i] stringValue];
		else
			[arguments addObject:[flagTemp stringByAppendingString:[[NSNumber numberWithDouble:outlets objectAtIndex:i] doubleValue stringValue]]];
	}
////Done
	[flagTemp release];
	printf("Args: %s\n", arguments description] UTF8String]);
	
	[task setArguments:arguments];
	[arguments release];
	
	[defaultCenter addObserver:self selector:@selector(taskCompleted:) name:[[NSTaskDidTerminateNotification object:task];

	[task launch];
	printf("Launched %s\n", [task arguments] description] UTF8String]);
	if([task isRunning])
		printf("RUN\n");
}



The [task isRunnng] check will print, but I added another button to the GUI that if you click on it, does the same thing (check if the task is running, print the result). It always gives me 'no'. So somehow, the [[NSTask is launching but stopping really quickly. Yet neither taskCompleted nor taskDataAvailable are ever getting triggered. The NotificationCenter I'm using is a globally declared one. The arguments are printing out just fine. I've also looked at the launch path via the debugger and it's good too.

The executable for the UNIX app is included in the project via Add to Project..., with the Copy Items Into Destination Folder option checked.

----

This does beg the question of whether there's something funky in the task itself. I am assuming here that     flags is an instance variable that you are
using in     runFoo:, and that it is some sort of array. Just incidentally, if it is an array, why aren't you using NSEnumerator to visit its elements?
(it might even help you debug...?)

For that matter (just curious) how are you getting away with declaring     i in the middle of a block of code? My compiler doesn't let me do that.

Furthermore, your selector     taskDataAvailable: is somewhere else and we can't inspect it, either. I know it sounds a little peevish, but there are a number of things you have not disclosed here, and we have no way of guessing whether or not they might contain errors.

----
Flags is an array of strings that contains the flags for the task ("-fn", "-o", etc.). The flags get merged with the values from the GUI to form the final arguments, i.e, ("-fn newfile", "-o savefile", etc.) As for the i...dunno. Compiler never said anything about it. I do it a lot. NSEnumerator, I guess it's just my habit to do the for loop. 
I hadn't included taskDataAvailable because I know it's never getting called: there's a print statement right at its beginning that I never see. Same with taskCompleted. I've included taskDataAvailable below though; all taskCompleted does right now is print 'Done', which I have yet to see:

    

-(void)taskDataAvailable:(NSNotification *)notif
{
	printf("Data available\n");
	NSData * incomingData = notif userInfo] objectForKey:[[NSFileHandleNotificationDataItem];

//If there's data
    if (incomingData && [incomingData length])
    {
///Print it to the NSTextView in the GUI
		NSString *incomingText = [[NSString alloc] initWithData:incomingData encoding:NSASCIIStringEncoding];
		if([textBox textStorage] mutableString] length] != 0)
			[[[textBox textStorage] mutableString] insertString:incomingText atIndex:([[[textBox textStorage] mutableString] length] -1)];
		else
			[[[textBox textStorage] mutableString] insertString:incomingText atIndex:0];
		[textBox setFont:[[[NSFont fontWithName:@"Monaco" size:10.0]];
		[textBox scrollPoint:NSMakePoint([textBox bounds].size.width, [textBox bounds].size.height)];		
////Then go back to watching for output 
		[taskOutput readInBackgroundAndNotify];
		[incomingText release];
		
        return;
    }
}

Edit: Maybe I should mention: I said I just did another wrapper where this stuff worked fine...the only difference between the two is that this one is a document-based application, if that matters.

----

You bet your ascii it matters.

You didn't comment on whether     flags is an instance variable - but since you declare that the flags are present where they are supposed to be,
I guess that is not an issue. What you leave out is any characterization of the task itself. If it is code that runs fine on the command line or as
a background process, then I guess that rules it out as a suspect. But you seem unwilling to say anything besides "my wrapper worked fine
on another task". If you are now document-centered, there is no reason that you can't run a task for each document instance in the background.

I would at least ask you to make sure that your task initialization is taking place in     windowDidLoad: etc. - but since
you are calling these initializations in an action that is presumably part of your document class, I guess that is settled.
You evidently want a control in your document window to launch the task *whatever the heck it is*.

And speaking of which, how do you terminate and release the task that you instantiate in the     initializeTask: method??????????

I think what you may want to go for is an application-wide control window (from MainMenu.nib) that launches tasks for you by opening a new document,
one new document for each task you want to run. Then in each document, you need to initialize the single task for which it is responsible.
But I'm shooting from the unhip here.

