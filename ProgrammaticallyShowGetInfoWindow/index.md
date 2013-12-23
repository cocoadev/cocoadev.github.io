---
layout: page
title: ProgrammaticallyShowGetInfoWindow
---

I noticed that neither NSWorkspace nor NSFileManager appear to have any way with which to programatically display the Finder's "Get Info" window. This seems a bit of an oversight (either on my part or on Apple's, your pick!) so I present for your consumption a category off NSWorkspace for programmatically displaying said window given any file or folder path. As with other code I've posted here, I've used my own whitespace/brace style rather than Apple's. -JonathanGrynspan

NSWorkspace+GTAdditions.h
    
#import <Cocoa/Cocoa.h>

@interface NSWorkspace(GTAdditions)
/* returns YES if the window was successfully displayed, NO otherwise */
- (BOOL)gt_openInformationWindowForFile: (NSString *)path;
@end


NSWorkspace+GTAdditions.m
    
#import "NSWorkspace+GTAdditions.h"

static NSString *getInfoScript = 
	@"tell application \"Finder.app\"\n"
		"open information window of %@ (\"%@\" as POSIX file)\n"
		"activate\n"
	"end tell";

@implementation NSWorkspace (GTAdditions)
- (BOOL)gt_openInformationWindowForFile: (NSString *)path {
	NSMutableString *command;
	NSAppleScript *script;
	id fileType;
	BOOL result;
	
	fileType = [[[NSFileManager defaultManager] fileAttributesAtPath: path traverseLink: NO] fileType];
	if (fileType) {
		command = path mutableCopy] autorelease];
		[command replaceOccurrencesOfString: @"\\" withString: @"\\\\" options: 0 range: [[NSMakeRange(0, [command length])];
		[command replaceOccurrencesOfString: @"\"" withString: @"\\\"" options: 0 range: NSMakeRange(0, [command length])];
		if ([fileType isEqual: NSFileTypeDirectory] && ![self isFilePackageAtPath:path]) {
			command = [NSMutableString stringWithFormat: getInfoScript, @"folder", command];
		} else {
			command = [NSMutableString stringWithFormat: getInfoScript, @"file", command];
		}
	} else {
		command = nil;
	}
	
	result = NO;
	if (command) {
		script = [[NSAppleScript alloc] initWithSource: command];
		if ([script executeAndReturnError: NULL])
			result = YES;
		[script release];	
	}

	return result;
}
@end


----
For most workspace related operations, Applescript tends to be the best to use - because of its wide range of functions. A lot of things missing from the NSWorkspace class are actually available via this method. Although not being the most desirable, it gets the job done.
----

