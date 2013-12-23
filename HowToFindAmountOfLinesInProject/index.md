---
layout: page
title: HowToFindAmountOfLinesInProject
---

One day I wanted to find out how many lines i wrote in a project, so I wrote this small thing, its not recursive, but it works none the less (my source files are all in the same folder).

    
#import <Cocoa/Cocoa.h>


@interface LineCountController : NSObject {
	id resultField;
}
- (IBAction)chooseDirectoryAndCountLines:(id)sender;
@end

#import "LineCountController.h"


@implementation LineCountController

- (IBAction)chooseDirectoryAndCountLines:(id)sender
{
	NSOpenPanel * panel = [NSOpenPanel openPanel];
	[panel setCanChooseFiles:NO];
	[panel setCanChooseDirectories:YES];
	[panel runModalForTypes:nil];
	
	NSArray * selectedFiles = [panel filenames];
	
	int linecount = 0;
	
	int i;
	for (i=0; i<[selectedFiles count]; i++)
	{
		NSFileManager * manager = [NSFileManager defaultManager];
		NSDirectoryEnumerator * directoryFiles = [manager enumeratorAtPath:[selectedFiles objectAtIndex:i]];
		
		NSString * file;
		while (file = [directoryFiles nextObject]) {
			if (file pathExtension] isEqualToString:@"m"] ||
				[[file pathExtension] isEqualToString:@"h"] ||
				[[file pathExtension] isEqualToString:@"c"]) {
				[[NSLog(selectedFiles objectAtIndex:i] stringByAppendingPathComponent:file]);
				[[NSString * contents = [NSString stringWithContentsOfFile:selectedFiles objectAtIndex:i] stringByAppendingPathComponent:file;
				NSArray * separated = [contents componentsSeparatedByString:@"\n"];
				
				NSLog(@"Count: %i",[separated count]);
				linecount += [separated count];
			}
			
		}
	}
	
	NSLog(@"Count: %i",linecount);
	
	[resultField setStringValue:[NSString stringWithFormat:@"%i",linecount]];
}

@end


If someone would want to make this recursive that would be neat, but it doesn't matter to me. Have fun :^).

----

Wouldn't it be easier to open a terminal window and type this? Or save it as a script file?

<code>
wc -l `find <myProjectDir> -type f -name "*.m" -or -name "*.h" -or -name "*.c"`
</code>

--TimHart

----

Well that is all fine and dandy if you know about that, I didn't so I wrote my own :^) .

If every source file is at the top-level, a "wc -l *.[hm]" will do the job.

----

some constructive criticisms:


* NSDirectoryEnumerator is recursive, so your app is recursive.
* objectAtIndex: takes an unsigned (int), not an (signed) int.
* I usually use NSEnumerator rather than NSArray's obj<nowiki/>ectAtIndex:. even though I don't normally use this feature, polymorphism is my friend.
* even if you do decide to stay with objectAtIndex:, you don't need to call it more than once (and shouldn't anyway, especially in the case of mutable arrays). assign it to a variable and use that.
* NSArray's count method returns an unsigned (int), not an (signed) int. thus, the correct printf/initWithFormat/NSLog format is %u.
* I would use an NSSet containing the three pathname extensions. this has three advantages:

* only one call to test membership
* only one call to extract the pathname extension (not a cheap operation - it creates a string, and you're doing that three times)
* membership-testing is a hash-table operation, so it may be (slightly) faster



*--boredzo*

Of course, the extracting the pathname extensions (and the other performance nits listed above) are totally drawfed by having an NSString object being created for every line in the file.  It'd be hugely faster to just spin through the string, use characterAtIndex: and count the newlines.

----

Very interesting. A while ago I had written my own function to do this exact same thing. Our code is quite similar:
    
int totalLines(NSString *path)
{
	NSFileManager *fileManager = [NSFileManager defaultManager];
	NSString *newPath = [path stringByStandardizingPath];
	NSArray *files = [fileManager subpathsAtPath:newPath];
	NSEnumerator *e = [files objectEnumerator];
	NSString *file;
	int c = 0;
	
	while (file = [e nextObject])
	{
		NSString *filePath = [newPath stringByAppendingPathComponent:file];
		BOOL isDir;

		if ([fileManager fileExistsAtPath:filePath isDirectory:&isDir] && !isDir)
		{
			if (
				file pathExtension] isEqualToString:@"h"] ||
				[[file pathExtension] isEqualToString:@"m"] ||
				[[file pathExtension] isEqualToString:@"pl"]
				)
			{
				[[NSString *str = [NSString stringWithContentsOfFile:filePath];
				NSArray *lines = [str componentsSeparatedByString:@"\n"];
				c += [lines count];
			}
		}
	}
	
	return c;
}


----

Often what is interesting is the number of statements rather than the number of lines.  You usually don't want to count blank lines or boiler plate comments etc.

<code>
find <projectdir> -type f -name "*.[cmh]*" -exec grep "[;\{]." {} \; | wc -l
</code>

This command does the following:
Recursively find all files within projectdir that are likely to be source files.
For each file found, print only lines that contain a ';' character or a '{' character.
Count the number of lines printed.

Counting semicolons and open curly braces is a good approximation of counting statements because all non-compound statements end with ';' and all compound statements begin with '{'.  I have also found that just counting '{' is a good metric of the complexity of a module/class implementation.

Note: Perhaps the utility the Unix shell(s) is now more apparent.

*Um, in a project with over 10,000 lines of 'text', it says there are only 206 statements. I'm going to take the safe bet and say that's not quite right. ;-)* **In my 38,000+ lines of code project, it said there were only 1394 statements. hehe**

There is an extraneous period in the regex which is causing it to only accept semicolons or open curly braces which are followed by another character, which is usually not the case (they're normally found right at the end of a line). Also, I believe the command should be egrep rather than grep in order to properly use regular expressions. This command seems to work better:
    
find . -type f -name "*.[cmh]*" -exec egrep '[;{]' {} \; | wc -l

 
----

Maybe SLOCCount may be the tool for you. http://www.dwheeler.com/sloccount/

