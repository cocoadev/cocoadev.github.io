---
layout: page
title: RTFDWithSubversion
---

I recently had need to add Subversion support to my app, which uses RTFD to store certain files, so I thought my solution might be of use to others.

THE PROBLEM:

My app uses a package as its file format, which stores lots of individual RTFD files representing different text documents the user has created within my rich-text program. Some of my users wanted to use Subversion or CVS to collaborate on projects using my program. However, the RTFD file format is not very amenable to Subversion or CVS. The problem is that RTFD files are usually saved like this:

    

NSFileWrapper *rtfdWrapper = textView textStorage] [[RTFDFileWrapperFromRange:all documentAttributes:nil]; 
[rtfdWrapper writeToFile:path atomically:YES updateFilenames:YES]; 



The "problem" is that NSFileWrapper completely overwrites what was already there. Subversion and CVS work by placing a hidden .svn or .cvs folder *inside* the RTFD directory, so when the above gets called, the .svn or .cvs folder gets annihilated, and all versioning information for the RTFD file is lost.

THE SOLUTION

The obvious solution for this is to write an NSFileWrapper category that checks for the existence of a .cvs or .svn directory and preserves it if necessary. So, here is my implementation of such a category:


NSFileWrapper_SVNAdditions.h:

    

#import <Cocoa/Cocoa.h>

@interface NSFileWrapper (SVNAdditions)

- (BOOL)writeToFile:(NSString *)path atomically:(BOOL)atomicFlag updateFilenames:(BOOL)updateNamesFlag preserveSVN:(BOOL)preserveSVNFlag;

@end



NSFileWrapper_SVNAdditions.m:

    

#import "NSFileWrapper_SVNAdditions.h"


@implementation NSFileWrapper (SVNAdditions)

- (BOOL)writeToFile:(NSString *)path atomically:(BOOL)atomicFlag updateFilenames:(BOOL)updateNamesFlag preserveSVN:(BOOL)preserveSVNFlag
{
	if (preserveSVNFlag == NO)
		return [self writeToFile:path atomically:atomicFlag updateFilenames:updateNamesFlag];
	
	NSFileManager *fileManager = [NSFileManager defaultManager];
	
	NSString *svnOrCvsPath = [path stringByAppendingPathComponent:@".svn"];
	
	// Check to see if the wrapper contains an .svn directory. If not, check to see if it contains a .cvs directory.
	if ([fileManager fileExistsAtPath:svnOrCvsPath] == NO)
	{
		svnOrCvsPath = [path stringByAppendingPathComponent:@".cvs"];
		
		if ([fileManager fileExistsAtPath:svnOrCvsPath] == NO)
			svnOrCvsPath = nil;
	}
	
	// If the wrapper contained an .svn or .cvs directory, cache it.
	NSFileWrapper *svnCache = (svnOrCvsPath != nil ? [[NSFileWrapper alloc] initWithPath:svnOrCvsPath] : nil);
	
	BOOL success = [self writeToFile:path atomically:atomicFlag updateFilenames:updateNamesFlag];

	// If we cached an .svn or .cvs directory, restore it now.
	if (svnCache != nil && svnOrCvsPath != nil)
	{
		[svnCache writeToFile:svnOrCvsPath atomically:YES updateFilenames:NO];
		[svnCache release];
	}
	
	return success;
}

@end




Using this category, the RTFD file is written as follows:

    

SFileWrapper *rtfdWrapper = textView textStorage] [[RTFDFileWrapperFromRange:all documentAttributes:nil]; 
[rtfdWrapper writeToFile:path atomically:YES updateFilenames:YES preserveSVN:YES];




This preserves any .svn or .cvs directories that have been created inside the RTFD package, and makes the RTFD format viable for users of Subversion or CVS.

I hope that is of use to somebody else. :)
KB

