---
layout: page
title: LSLaunchFlags
---

Below is a code where I am trying to launch "TextEdit.app" in the Classic emulation environment. It works fine for Mac OS X 10.4.8 but not for Mac OS X 10.4.10 and later versions.
I didn't find any apple document why it happens.

    
LSLaunchURLSpec spec;
OSStatus err;
	
spec.appURL			= (CFURLRef)[NSURL fileURLWithPath:@"/Applications/TextEdit.app"];
spec.itemURLs		= nil;
spec.passThruParams	= nil;
spec.launchFlags           = kLSLaunchInClassic; /*Requests that the application be forced to launch in the Classic emulation environment. Available in Mac OS X v10.0 and later.*/
spec.asyncRefCon	        = nil;
	
err = LSOpenFromURLSpec( &spec, nil );
if( err )
		NSLog(@"Error = %@",[NSError errorWithDomain: NSOSStatusErrorDomain code: err userInfo: nil]);


Check in Console to see the error. It displays
"Error = NSError "Mac OS error -10828" Domain=NSOSStatusErrorDomain Code=-10828".

While debugging I saw the "err" field summary as "[kLSNoClassicEnvironmentErr] (The Classic environment was required but is not available)".

I thought apple does not provide classic emulation environment for newer versions of "Tiger". So "kLSLaunchInClassic" flag doesn't work here. If it doesn't is there any way to launch that application in classic emulation environment?

But as per the apple document that flag is available in Mac OS X v10.0 and later.
----
I hate to be seemingly unhelpful, but why, WHY would you do this? Classic is dead. It's been six years since the release of Mac OS X. Most Mac users (and I'm making an educated guess here) don't own a single Classic app, and many of them wouldn't really understand the difference. Is there some little-used feature you need from Classic? Because you'd probably be more productive and useful trying to implement *that*. --JediKnil

----
Yes I need some little feature from Classic. Actually above code is an example that I had mentioned. I have to launch "Automator Launcher.app"  which is in "/System/Library/CoreServices/Automator Launcher.app" using LSOpenFromURLSpec. Because I have to run an automator workflow programmatically and the solution was found in "http://www.rogueamoeba.com/utm/posts/Article/automator-hosting-2005-06-03-03-00". 

You can go through that document and work around it you will get two files named "PTAutomatorWorkflow.h" and "PTAutomatorWorkflow.m" which execute automator workflow by giving the path of workflow file. There is an API called "+ (NSError*)executeWorkflow: (NSString*)workflowPath withFiles: (NSArray*)filePaths" pass first argument as path of your workflow and nil for second argument that's it and execute the program. 

While executing you will see the menu bar status at right side indicating the workflow status. You will see that workflow status in the menu bar if we provide other than "kLSLaunchInClassic" flags. Now what I want here is, don't want to put up the workflow status in the ""Menu Bar"  so  I used the flag "kLSLaunchInClassic". This works fine for Mac OS X 10.4.8. But As I updated my system to Mac OS X 10.4.10 it throughs as error as I mentioned above as, "[kLSNoClassicEnvironmentErr] (The Classic environment was required but is not available)". So I don't get the solution for it.

----

You're trying to launch an Objective-C program in the Classic emulator just to avoid it putting text in the menu bar?

----

Yes. 
Is there any other way to do it? I want to avoid the text in menu bar.
Did you check out the files of PTAutomatorWorkflow.

----
You probably can't avoid it. Trying to launch in Classic certainly isn't a viable workaround. Consider the fact that Classic does not exist on any Intel Mac, and Classic was not installed on most PPC Macs. You basically stumbled across a bizarre side effect of a ridiculous request, and now you expect that bizarre side effect and ridiculous request to work everywhere. It's just not going to happen.

----

This is a trick question, right?

TextEdit.app is a Cocoa application, it will not (and never has) run in Classic.
This is what is commonly known as "programmer error."

