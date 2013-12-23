---
layout: page
title: ReadingListOfFilesInTheBackground
---

Hi I am trying to get a listing of a folder using NSFileManager's subpathsAtPath command.  Then when I get a listing in a NSArray I use loops to weed out certain file paths.  After that I use copyPath to copy the files to a new location.  All this works fine but when I do it everything in the GUI freezes.  Nothing is responsive.  Even when I put in ways of updating information through NSProgressIndicator and NSTextFields as shown below, it still does not work.  Is there anyway to run these commands in the background or in a way so the GUI will still be responsive?


Thanks

Gabe

NSFileManager *manager = [NSFileManager defaultManager];
	
NSArray *homeFiles=[manager subpathsAtPath:path];

for (x=1;x<=[newFiles count];x++){

	[backupStatusBar incrementBy:(100/[newFiles count])];

	[statusField setStringValue:[@"Copying: " stringByAppendingString:[newFiles objectAtIndex:x-1]]];

	[manager copyPath:[path stringByAppendingString:[newFiles objectAtIndex:x-1]] toPath:[destination stringByAppendingPathComponent:[newFiles objectAtIndex:x-1]] handler:nil];

	}

----

You may want to look into ThreadWorker since it is an easy way to run background operations and still get notifications when they finish.

--JeffDisher

----

I've said this before, but I'll say it again.  I strongly recommend against using either NSFileManager or NSWorkspace for copying files.  For example, I never got NSFileManager's     -copyPath: to work in a secondary thread; it would either crash or return immediately after copying only a handful of items.  What does work well is FSCopyObject  (http://developer.apple.com/samplecode/FSCopyObject/FSCopyObject.html ), a bit of sample code for doing filtered directory copies.  As a bonus, it supports **all** HFS+ attributes and it's blazingly fast (even more so if you turn up kDefaultCopyBufferSize in the code)  -- Bo

