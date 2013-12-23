---
layout: page
title: FilePathstoOrderedDictionary
---

I've been trying to find a way to access either a directory, or a text file with a list of file paths and be able to order them in to a hierarchical list in an NSDictionary.

For instance, if I open a folder that contains one file, my dictionary would look something like this:

    
"Path" = "/Path/To/Root/Folder",
"Is Directory" = YES,
"Contents" = { "Path" = "/Path/To/Root/Folder/File",
               "Is Directory" = NO }


I haven't even begun to tackle the ordering of a text file of paths, but I did get a start on traversing through a folder.  I ran into a problem, though...

My code (shown below) runs fine for a folder that contains no more than around 240 subfiles/subfolders, but once it gets close to 240, it errors out and says that NSMutableDictionary object:forKey: cannot insert nil object.  I'm thinking that the problem might be that my method calls itself for each new file/folder it finds and maybe I can only do that so many times?  I'm guessing I'm going about it in sort of a loopy way, but I couldn't think of another way to do it.  I tried using NSFileManager's subpathsAtPath: method as well, but I still had to work my way through the list and so when I got to around 240 it errored out again.

Any help would be appreciated.

- Corporate Newt


    
- (NSDictionary *)travelPath:(NSString *)path {
	if (!path) { return nil; }
	
	path = [path stringByExpandingTildeInPath];
		
	NSMutableDictionary *orderedFiles = [[NSMutableDictionary alloc] init];
	NSFileManager *f = [NSFileManager defaultManager];
	NSWorkspace *w = [NSWorkspace sharedWorkspace];
	NSString *dName = [f displayNameAtPath:path];
	
	if (path) { [orderedFiles setObject:path forKey:@"Path"]; }
	
	if (dName) {[orderedFiles setObject:dName forKey:@"Name"];}
	else {[orderedFiles setObject:@"" forKey:@"Name"];}
	
	BOOL dir;
	
	[f fileExistsAtPath:path isDirectory:&dir];
	
	[orderedFiles setObject:[NSNumber numberWithBool:dir] forKey:@"Directory"];
	
	if (dir) {
		NSArray *folderContents = [f directoryContentsAtPath:path];
		
		NSMutableArray *rootContents = [[NSMutableArray alloc] init];
		
		int i;
		for (i=0; i<[folderContents count]; ++i) {
			NSString *fullPath = [path stringByAppendingPathComponent:[folderContents objectAtIndex:i]];
			NSDictionary *current = [self travelPath:fullPath];
			[rootContents addObject:current];
		}
		[orderedFiles setObject: rootContents forKey:@"Contents"];
	}
		
	return orderedFiles;
	
}

----
The term you are looking for is "Recursion".  It means that a function or method directly or indirectly calls itself.  Indeeed, if recursion becomes too deep, the system stack is corrupted and the usual result is a segmantation fault.  The error you describe is probably not due to recursion.

Why didn't you just use - (NSDirectoryEnumerator *)enumeratorAtPath:(NSString *)path  "Because the enumeration is deep�that is, it lists the contents of all subdirectories�this enumerator object is useful for performing actions that involve large file-system subtrees. "

Apple even provides a sample right in the documentation:

    
NSString *file; 
NSString *docsDir = [NSHomeDirectory() stringByAppendingPathComponent:  @"Documents"]; 
NSDirectoryEnumerator *dirEnum = 
    [[NSFileManager defaultManager] enumeratorAtPath:docsDir]; 
  
while (nil != (file = [dirEnum nextObject])) { 
    if (file pathExtension] isEqualToString: @"doc"]) { 
        [self scanDocument: [docsDir stringByAppendingPathComponent:file; 
    } 
} 


http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSFileManager_Class/Reference/Reference.html

----

That is what I tried doing in the first place, and it lists all the files correctly, but I'm not sure how to break up the paths and organize them in a similar fashion as my above code.  Using NSFileManager's enumeratorAtPath (or even the subpathsAtPath) method gives all the right information, but I'm not sure how to tackle the next step.

- Corporate Newt
----
See NSString -lastPathComponent and friends like -pathComponents which are linked directly from the NSFileManager documentation:
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html#//apple_ref/occ/instm/NSString/lastPathComponent

See also: http://developer.apple.com/documentation/Cocoa/Conceptual/LowLevelFileMgmt/Tasks/LocatingDirectories.html

----

Thanks for all the help, I'll take some time and see what I can crank out.

- Corporate Newt

