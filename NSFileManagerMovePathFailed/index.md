---
layout: page
title: NSFileManagerMovePathFailed
---



How do i catch the error given by NSFileManager movePath ?
I don't know how to use handlers or anything, i just know that sometimes the action failes and it would be nice to know what is the cause of it

----

The fine manual tells all:

*The argument handler identifies an object that responds to the callback messages fileManager:willProcessPath: and fileManager:shouldProceedAfterError: (see "Methods Implemented by the Delegate"). This callback mechanism is similar to delegation. NSFileManager sends the first message when it begins a copy, move, remove, or link operation. It sends the second message when it encounters any error in processing.*

In other words, pass an object in that can respond to those two methods. It will receive the error that caused the failure, and from there you can do anything you want to do with it.

----

I implemented 
    
+ (BOOL)fileManager:(NSFileManager *)manager
shouldProceedAfterError:(NSDictionary *)errorDict
{
    int result;
    result = NSRunAlertPanel(@"program",
        @"File operation error: %@ with file: %@",@"Proceed", @"Stop", NULL,
        [errorDict objectForKey:@"Error"],
        [errorDict objectForKey:@"Path"]);

    if (result == NSAlertDefaultReturn)
        return YES;
    else
        return NO;
}

+ (void)fileManager:(NSFileManager *)fm willProcessPath:(NSString *)path
{
}

but i don't get any alertpanels although i know there are errors... 
i write [self class] as handler..
----
Looks fine to me... Can you post the code that runs the operation? Also, how do you know that there are errors?

----
    
[fm movePath: aFullPath toPath: targetPath handler:[self class]];


Because if i put the command with an encapsulating if(![fm...]) it enters it

----

And you've tested this code with paths that should work to verify that the code works properly in that case?

I'm pretty much out of ideas. One more thing, though; the handler methods are intended to be instance methods, so it might be worth trying that. Make your handler methods instance methods and just pass self instead of     [self class]. It's cleaner from a design point of view anyway. This shouldn't affect anything, but it's the only thing I can think of.

----

Usually when I have problems with NSFileManager operations, it's because of a screwed up path. Use NSLog() to write out the paths you're passing to NSFileManager so you're sure they're valid. They should be full paths - in other words, "~/Documents/Mydoc.txt" is an invalid path; you'd need to use [path stringByExpandingTildeInPath] or [path stringByStandardizingPath] to make sure you get something like "/Users/me/Documents/Mydoc.txt". Log it out and make *SURE* the paths are valid. If you haven't done this yet and are still experiencing problems, you kind of deserve it. ;-)

----

Trust me, i put out everything with nslog atm. Just trying to understand all the data :P

*K. Would you mind posting the results? Another pair of eyes can be a great help.*

----

Thanks, but atm i think it has something to do with the arrays im trying to use with the renaming procedure.. 
i try to empty my array with [myfileList removeAllObjects] since im refilling it, but im not sure it's working.. 
in my init i declare myfileList = [[NSMutableArray alloc] init]; , am i doing something wrong or am i on the wrong track ?

----

How are you refilling the array? The code you posted seems OK.

----

With a loop, looping through a folder and using [myfileList addObject: currentItem];
However the i'm using _LSCopyAllApplicationURLs with all this. so myfileList is a cleaner version of the urls recieved from _LSCopyAllApplicationURLs, but ill try to explain the situation at the moment:

when im running my algorithm for moving the files, it says the action went alright:
    
if(!{fm movePath: aFullPath toPath: aFullPathUnhide handler:nil]){
 NSLog(@"failed");
} else {
 NSLog(@"succeeded");
}


but after running _LSCopyAllApplicationURLs the files still exist in the original place. So im wondering if the movePath works or if _LSCopyAllApplicationURLs is slow...

*you do have proper permissions to be able to remove the items, yes?*

----

Yea because it workes if i restart the program or try the action again, a bit hard to explain without showing the whole program

----

I've followed the above and am still having trouble.
My Code

    

	NSString *filename = dictionaryName stringValue] stringByStandardizingPath];
	[[NSString *homeDir = @"/Users/mat";
	//get the location of the dictionary
	NSString *applicationSupportFolder =
        [[[NSFileManager defaultManager] findSystemFolderType:kApplicationSupportFolderType forDomain:kOnSystemDisk] stringByStandardizingPath];
	NSString *newLocation = [[NSString stringWithFormat:@"%@/%@/Words/edicts/%@", homeDir, applicationSupportFolder, filename] stringByStandardizingPath];
	NSString *originalLocation =edictLocation stringValue] stringByStandardizingPath];
	[[NSLog(@"%@", newLocation);
	NSLog(@"%@", originalLocation);
	if([[NSFileManager defaultManager] movePath:originalLocation toPath:newLocation handler:nil]){
		NSLog(@"Copied to: %@", newLocation);
	}



Produces in the Log
/Users/mat/Library/Application Support/Words/edicts/jedict

/Users/mat/dev/edict2/edict

jedict comes from the user interface. The file isn't copied. Anyone know why? The source certainly exists and I believe I have permission to it:

[Mathieu:~] mat% ls -l  /Users/mat/dev/edict2/ 

total 24488

-rw-r--r--   1 mat  margie  6398728 Sep  7 00:25 edict


----
Pornbably unrelated, but in the code at the beginning of this (old) thread, the poster is defining class methods for the handler, and it is not entirely clear that this would work properly. You probably want to have instance methods, and pass a "real" object as the handler, not a class object.

----
That should be fine. At runtime, a class is just an object, and the class methods are the methods it responds to. There's no difference between a class object with a     +foo class method, and an instance of a class with a     -foo instance method.

----
I know that, and I realize it should work (although it would probably be considered bad design) but I wonder if the initial poster set it up properly. Well, the question was posted a year ago, so probably too late for him. But anyone reading this page might be misled by the code example.

