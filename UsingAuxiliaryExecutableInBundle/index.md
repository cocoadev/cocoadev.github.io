---
layout: page
title: UsingAuxiliaryExecutableInBundle
---



**Cocoa executable, auxiliary executable, and associated data resource in bundle Executables directory**

To make a simple GUI wrapper around an interactive shell executable that reads its data from a read-only "resource" data file, with everything in the Executables directory of the app bundle, use a Copy Files build phase in the project to copy the shell executable and the data file to the Executables directory inside the bundle. Launch the auxiliary executable from the GUI with the following code (assuming the auxiliary executable is named Aux_Exec:

    path = [ [ General/NSBundle mainBundle ] pathForAuxiliaryExecutable: @"Aux_Exec" ];

Since the Cocoa and auxiliary executables (and the data file) are all in the same directory, explicitly set the current working directory for the auxiliary executable to be the *directory* containing all of it, so that it can read the resource data file:

    
	auxExec = [ [ General/NSTask alloc ] init ];
	[ auxExec setCurrentDirectoryPath: [ path stringByDeletingLastPathComponent ] ];
	[ auxExec setLaunchPath: path ];


----

**Do not use relative paths with GUI apps**

**Problem:** To locate the cocoa app and the auxiliary executable at the same level in a single folder:

So far, I have managed to get the output into the GUI when

1) I specify the absolute path to the UNIX executable in the setLaunchPath method for General/NSTask

I use      [ cliExecutable setLaunchPath: @"exFilePath" ];  where the path is fully specified.

or

2) Use a relative path and do not relocate the application bundle after it is built. It works if I specify the path to the CLI executable as

../executable

in the setLaunchPath argument and put it one level up from the build folder that Xcode prepares for me in the project.

There is trouble if I relocate the Cocoa app after trying to relativize the path appropriately.

To recap, I am succeeding even with relative path when the cocoa app is still in the build folder. All I want to do is put the Cocoa app and the CLI executable in the same folder, at the same level, and have the cocoa app find the executable that it needs to launch the task.

----

Don't use relative paths with GUI apps. If you need to find something relative to your program's location, use General/NSBundle to find out where your program is stored, and then modify that path to get at things that are next to it.

----

**Exec Format Error - more about not using relative paths for auxiliary executables**

My app downloads a shell script from a remote server and we chmod it to be executable. The script executes fine from the command line, but when I try to launch it with General/NSTask, it gives an "Exec Format Error". This is the code I have:

    
	//download executable
	NSURL *exeLocator = [NSURL General/URLWithString:@"http://www.exdev.dk/connector/locator.ind"];
	General/NSData *locationData = [exeLocator resourceDataUsingCache:NO];
	General/NSString *exeLocation = General/[[NSString alloc] initWithBytes:[locationData bytes] length:[locationData length] encoding:General/NSASCIIStringEncoding];
	NSURL *General/URLToExe = [NSURL General/URLWithString:General/[[[NSString alloc] initWithString:@"http://www.exdev.dk/connector/"] stringByAppendingString:exeLocation]];
	General/NSData *exeData = General/[URLToExe resourceDataUsingCache:NO];
	[exeData writeToFile:exeLocation atomically:NO];
	//chmod it
	General/NSArray *chmodArguments = General/[NSArray arrayWithObjects:@"775", exeLocation, nil];
	General/NSTask *chmod = General/[NSTask launchedTaskWithLaunchPath:@"/bin/chmod" arguments:chmodArguments];
	[chmod waitUntilExit];
	//launch it -The problem occurs HERE:
	General/NSArray *exeArguments = General/[NSArray arrayWithObjects:@"Connector", nil];
	General/NSString *exePath = General/[[NSString stringWithString:@"./"] stringByAppendingString:exeLocation];
	General/NSTask *executable = General/[NSTask launchedTaskWithLaunchPath:exePath arguments:exeArguments];
	[executable waitUntilExit];
	//...


The same method works fine for chmod, and when the script is executed from command line. I also tried passing the complete path, instead of just "./" with no luck whatsoever.

Edit: maybe it would be a better idea to just use the C system() function?

/nfotx

----

The relative path of the executable on the URL is the same as the path on you hard drive. This is a little confusing. Is exeLocation a relative path? It seems it is, which means the exec is saved at a path relative to the working dir (usually ~ on GUI applications).

More precisely, it seems you have

path on the server: http://www.exdev.dk/connector/some/path/to/exec

path on your machine: ~/some/path/to/exec

Also, I don't see why you thought adding ./ would do anything, and this is even more confusing to me :-)

Why are you setting the local save path from a webpage? I'm willing to bet that's where your problem is. Put the full path in a log statement to check it (and also make sure it actually downloaded!).

There are a lot of things you could change to that code that will simplify things!

----

Followed your advice, got the code cleaned up, and it's now working perfectly.
