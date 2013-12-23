---
layout: page
title: NSFileManagerHelp
---

I am attempting to make little cocoa applications to learn some basics. Currently I am having issues getting an NSMutableArray to recieve the list of directory items from NSFileManager.

Here is my code.

    
- (IBAction)actionLoadFolder:(id)sender
{
	// set open panel
	NSOpenPanel *oPanel = [NSOpenPanel openPanel];
	[oPanel setCanChooseDirectories:YES];
	[oPanel setCanChooseFiles:NO];
	
	// run the panel and set the result
	int result = [oPanel runModalForDirectory:nil file:nil types:nil];
	if(result == NSOKButton)
	{
		NSArray *pathToOpen = [oPanel URLs];
        NSURL *pathURL = [pathToOpen objectAtIndex:0];
		
		if([pathURL isFileURL])
		{
		
				// debug to console the path.
				NSLog(@"url is %@", pathURL);
				
			// for shits and giggles, lets attempt to get a list of files at that URL and just log them out?
				
				NSString * pathToOpenString = [pathURL absoluteString]; 
				
				NSFileManager *manager = [NSFileManager defaultManager];
				
				NSMutableArray *itemsInPathToOpen = [[NSMutableArray alloc] init];
			
				[itemsInPathToOpen setArray: [manager directoryContentsAtPath:pathToOpenString]];

			/*
				[itemsInPathToOpen addObject:@"/this/is/path/1"];
				[itemsInPathToOpen addObject:@"/this/is/path/2"];
				[itemsInPathToOpen addObject:@"/this/is/path/3"];
				[itemsInPathToOpen addObject:@"/this/is/path/4"];
				[itemsInPathToOpen addObject:@"/this/is/path/5"];
				[itemsInPathToOpen addObject:@"/this/is/path/6"];
			*/
				
				int i;
				int pathcount;
				pathcount = [itemsInPathToOpen count];
				
				NSLog(@"pathcount %d", pathcount);
				
				for(i = 0; i <= pathcount; i++)
				{
					NSString * item = [itemsInPathToOpen objectAtIndex:i];
					printf("%s \n" ,[item UTF8String] );
				}

		}	
		else
		{
			NSLog(@"Not a path URL");
		}	
	}
	else
	{
	
	}
}


This works if I un-comment the [itemsInPathToOpen addObject:@"path"]; so my error lies in how I am attempting to fill up my itemsInPathToOpen array from NSFileManager 'manager' instance?

Any help? This has been eluding me all day. Thanks.

----

This looks like it should work, but try switching the following pieces of code:
    
// Your code
NSMutableArray *itemsInPathToOpen = [[NSMutableArray alloc] init];
[itemsInPathToOpen setArray: [manager directoryContentsAtPath:pathToOpenString]];

// New code
NSMutableArray *itemsInPathToOpen = [[NSMutableArray alloc] initWithArray:[manager directoryContentsAtPath:pathToOpenString]];

// Other code that might work:
NSMutableArray *itemsInPathToOpen = manager directoryContentsAtPath:pathToOpenString] mutableCopy];

I don't know exactly why it should make a difference, but you can try it. --[[JediKnil

----

Thanks, but neither seem to work. Im pretty confused by this, to be honest, which usually means Im misunderstanding something...

----

What is the output you are expecting? What do you get instead? -- MikeAmy

----
Your code is wrong in many aspects:

a) you have a memory leak in itemsInPathToOpen, because you don't release it

b) pathToOpenString contains wrong formated path, because it contains file:// at the beginning and %20 escapes instead of spaces

c) [itemsInPathToOpen setArray: [manager directoryContentsAtPath:pathToOpenString]]; returns 0 objects because of b)

d)for(i = 0; i <= pathcount; i++) should be for(i = 0; i < pathcount; i++), otherwise the loop goes one more time than needed

the correct code is:
    
- (IBAction)actionLoadFolder:(id)sender
{
	// set open panel
	NSOpenPanel *oPanel = [NSOpenPanel openPanel];
	[oPanel setCanChooseDirectories:YES];
	[oPanel setCanChooseFiles:NO];
	[oPanel setAllowsMultipleSelection:YES];
	
	
	// run the panel and set the result
	int result = [oPanel runModalForDirectory:nil file:nil types:nil];
	if(result == NSOKButton)
	{
		NSArray *pathsToOpen = [NSMutableArray arrayWithArray:[oPanel filenames]];
		NSEnumerator *e = [pathsToOpen objectEnumerator];
		NSString *tempPath;
		NSFileManager *manager = [NSFileManager defaultManager];
		NSMutableArray *paths = [NSMutableArray arrayWithCapacity:5];
		
		while (tempPath = [e nextObject])
		{
			NSArray *currPathContent = [NSArray arrayWithArray:[manager directoryContentsAtPath:tempPath]];
			NSString *temp;
			NSEnumerator *pathE = [currPathContent objectEnumerator];
			
			while (temp = [pathE nextObject])
			{
				[paths addObject:[tempPath stringByAppendingPathComponent:temp]];
				NSLog(@"%@", [tempPath stringByAppendingPathComponent:temp]);
			}
			
		}
		
	}
}


HTH
BobC

----

BobC, thank you very much. Last night I noticed the memory leak and in the debugger that I was 'off by one', but my main issue was that I was feeding a 'malformed' path to NSFileManager. Thanks again! Im slowly learning all the class APIs and how to do things.. a lot to swallow. It seems using NSOpenPanel filenames method was the way to go. Thanks!

----

What about using NSDirectoryEnumerator?

