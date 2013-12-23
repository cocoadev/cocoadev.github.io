---
layout: page
title: SanityCheckingForNSMakeRangeExample
---

Here's the troublesome code:

    
-(void)initializeFileList{
	NSFileManager *theFileManager;
	NSDirectoryEnumerator *dirEnum;
	NSString *filePath, *fileName;
	NSString *comicNumber, *comicTitle;
	
	theFileManager=[NSFileManager defaultManager];
	[theFileManager changeCurrentDirectoryPath:@"/Users/owenyama/Documents/Comics"];
	dirEnum=[theFileManager enumeratorAtPath:[theFileManager currentDirectoryPath]];
	while(filePath=[dirEnum nextObject]){
		fileName=[filePath lastPathComponent];
		comicNumber=[fileName substringWithRange:NSMakeRange(0,4)];
		comicTitle=[fileName substringWithRange:NSMakeRange(5,[fileName length]-9)]; /* TROUBLE */
		[theFileList setObject:comicTitle forKey:comicNumber];
	}
}


This method is in my controller class, and it gets called from awakeFromNib. All the variables there are local except for theFileList, which is an NSMutableDictionary that is an instance variable of the class. In awakeFromNib, I initialize it, and that works fine.

What this code is intended to do: I have a bunch of GIF files in a folder on my hard drive (the path can be seen in the code). They all have names like: "0001 title.gif": four digits, a space, a title and the extension. As you can see, I'm going through the folder and parsing the filenames to get the title and number of each file. Then I store the titles in a dictionary with the numbers as keys.

When I run the project, it hangs for a bit, then crashes without ever showing the window. In the Run Log, I get the following message:

    
[Session started at 2005-04-23 22:24:31 +0200.]
*** malloc_zone_malloc[3223]: argument too large: 4294967294

Executable �ComicViewer� has exited due to signal 11 (SIGSEGV).


I tried setting a breakpoint and going through the code in the debugger. The problem seems to be on the line I've marked TROUBLE. The interesting thing is, this error only occurs if I have 452 or more items in theFileList (I have about 700 files to go through). So if I change the while statement to     while((filePath=[dirEnum nextObject]) && ([theFileList count]<452)), the code works properly. The dictionary (which I use to populate an NSTableView) has been filled properly.

Admittedly I'm working with quite a lot of data here, but I've worked with more in the past (like in the batch-renaming program I wrote to title the files properly) without problems. Anybody have insights? --OwenYamauchi

----

You really need to check the filename's length before you start doing substring operations on it. If the length is less than 9, then the second argument to NSMakeRange is going to wrap around and be a huge number, and NSString is probably choking on that (trying to allocate memory and not sanity-checking the arguments).

----

Yup, that was it. You guys are good. Thanks. --OwenYamauchi

