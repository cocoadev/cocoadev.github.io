---
layout: page
title: AuthenticatingNSFileManager
---




Hello,

All of the authentication examples I have found relate to running a UNIX command, or something.  I need to remove a file, then copy a file inside a directory not owned by the user:

    
NSFileManager * manager = [NSFileManager defaultManager];

[manager removeFileAtPath:@"some/directory/thats/write/protected/file.bla" handler:nil];
[manager copyPath:@"some/path/i/can/access/file.bla" toPath:@"some/directory/thats/write/protected/file.bla" handler:nil];


Is there any simple way to just prompt the user for authentication and let the above work on protected directories.  Using some of the advanced wrappers around the SecurityFramework seem like overkill...I really hope there is a simple solution to this.  I have looked around the various other pages, but they do not pertain to the file manager.  Any suggestions?  Thanks for any assistance.

----

    //
//  NSFileManagerAdditions.h
//  TRKit
//

#import <Cocoa/Cocoa.h>


@interface NSFileManager (TRAdditions)
- (NSString *)newTmpFilePath;
- (BOOL)authorizedMovePath:(NSString *)source toPath:(NSString *)destination;
- (BOOL)authorizedCopyPath:(NSString *)source toPath:(NSString *)destination;
@end
    //
//  NSFileManagerAdditions.m
//  TRKit
//

#import "NSFileManagerAdditions.h"
#import "NSApplicationAdditions.h"
#import <Carbon/Carbon.h>
#import <Security/Security.h>

static AuthorizationRef authorizationRef = NULL;

@implementation NSFileManager (TRAdditions)

- (NSString *)newTmpFilePath
{
	NSString * tmpDirectory = NSTemporaryDirectory();
	NSString * identifier = [NSApp applicationIdentifier];
	
	if (tmpDirectory == nil || identifier == nil) return nil;
	
	for (;;)
	{
		NSString * tmpFileName = [NSString stringWithFormat:@"%@_%i",identifier,RandomIntBetween(0,100000)];
		NSString * path = [tmpDirectory stringByAppendingPathComponent:tmpFileName];
		path = [path stringByStandardizingPath];
		
		if ([self fileExistsAtPath:path])
		{
			continue;
		}
		else
		{
			// 'Touch' a file here so that it guarentees that another won't be created (rare chance).
			//[self createFileAtPath:path contents:nil attributes:nil];
			return path;
		}
	}
}

- (BOOL)authorizedMovePath:(NSString *)source toPath:(NSString *)destination
{
	NSBundle * trkitBundle = [NSBundle bundleForClass:NSClassFromString(@"TRIntegration")];
	NSString * trkitResourcePath = [trkitBundle resourcePath];
	NSString * trkitMoveUtilityPath = [trkitResourcePath stringByAppendingPathComponent:@"move"];
	
	if (![self fileExistsAtPath:trkitMoveUtilityPath])
	{
		NSLog(@"Cannot find move utility.");
		return NO;
	}
	
	/* The move utlity exists, we can now procede. */
	OSStatus status;
	
	if (authorizationRef == NULL)
	{
		// Get Authorization.
		status = AuthorizationCreate(NULL,
									 kAuthorizationEmptyEnvironment,
									 kAuthorizationFlagDefaults,
									 &authorizationRef);
	}
	else
	{
		status = noErr;
	}
	
	// Make sure we have authorization.
	if (status != noErr)
	{
		NSLog(@"Could not get authorization, failing.");
		return NO;
	}
	
	// Set up the arguments.
	char * args[2];
	args[0] = (char *)source stringByStandardizingPath] UTF8String];
	args[1] = (char *)[[destination stringByStandardizingPath] UTF8String];
	args[2] = NULL;
	
	status = [[AuthorizationExecuteWithPrivileges(authorizationRef,
												trkitMoveUtilityPath stringByStandardizingPath] UTF8String],
												0, args, NULL);
	
	if (status != noErr)
	{
		[[NSLog(@"Could not move file.");
		return NO;
	}
	else
	{
		return YES;
	}
	
	return NO;
}

- (BOOL)authorizedCopyPath:(NSString *)source toPath:(NSString *)destination
{
	NSString * tempFile = [self newTmpFilePath];
	[self copyPath:source toPath:tempFile handler:nil];
	[self authorizedMovePath:tempFile toPath:destination];
}

@end

Small command line tool in resources (use this instead of the CLI utilities as it will work without the BSD subsystem):
    // move.m
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	{
		// Make sure we have 3 args (1: path 2: source 3: destination).
		if (argc != 3) return -1;
		NSString * source = [NSString stringWithUTF8String:argv[1]];
		NSString * destination = [NSString stringWithUTF8String:argv[2]];
		
		source = [source stringByStandardizingPath];
		destination = [destination stringByStandardizingPath];
		
		printf("Moving \"%s\" to \"%s\"...",[source UTF8String],[destination UTF8String]);
		
		NSFileManager * manager = [NSFileManager defaultManager];
		if (![manager fileExistsAtPath:source])
		{
			printf("failed\n***No source file at \"%s\".\n",[source UTF8String]);
			return -1;
		}
		
		[manager movePath:source toPath:destination handler:nil];
		
		printf("done\n");
	}
    [pool release];
    return 0;
}

The category is in my framework so you must change     [NSBundle bundleForClass:NSClassFromString(@"TRIntegration")]; to something that will work in your situation (I must use this since it is a category and not a new class).

*For an application,     [NSBundle mainBundle] will probably work fine.*

----

Thank you for your help.  Where is the RandomIntBetween() method defined?  Does it rely on the NSApplicatAdditions.h file? Thanks again.

----

Oh I forgot about those here they are:

    //
//  NSApplicationAdditions.h
//  TRKit
//

#import <Cocoa/Cocoa.h>


@interface NSApplication (TRAdditions)
+ (NSString *)applicationVersion;
- (NSString *)applicationVersion;
+ (NSString *)applicationIdentifier;
- (NSString *)applicationIdentifier;
+ (NSString *)applicationName;
- (NSString *)applicationName;
@end    //
//  NSApplicationAdditions.m
//  TRKit
//

#import "NSApplicationAdditions.h"


@implementation NSApplication (TRAdditions)

+ (NSString *)applicationVersion
{
	return [[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleVersion"];
}

- (NSString *)applicationVersion
{
	return self class] applicationVersion];
}

+ ([[NSString *)applicationIdentifier
{
	return [[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleIdentifier"];
}

- (NSString *)applicationIdentifier
{
	return self class] applicationIdentifier];
}

+ ([[NSString *)applicationName
{
	return [[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleExecutable"];
}

- (NSString *)applicationName
{
	return self class] applicationName];
}

@end    static __inline__ int [[RandomIntBetween(int a, int b)
{
    int range = b - a < 0 ? b - a - 1 : b - a + 1; 
    int value = (int)(range * ((float)random() / (float) LONG_MAX));
    return value == range ? a : a + value;
}

static __inline__ float RandomFloatBetween(float a, float b)
{
    return a + (b - a) * ((float)random() / (float) LONG_MAX);
}

----

ATTENTION: This code seems to make some problems when compiling for 64-bit.
I think it is saver to use mktemp (as suggested below).
There is a nice NSFileManager category on Github, which can be used for temp path generation.
http://github.com/AlanQuatermain/aqtoolkit/tree/master/TempFiles/

Perfect :)  The code works excellently, but I am trying to replace a file with something else�&#65533; would the easiest solution be to just move the file to the trash, or something, and then copy it?  Thanks.

*You could move it to /tmp (with some random numbers after it's name) just incase something happens to the file, /tmp is cleaned out every once in a while so unless it is a huge file it won't matter :) *

----

It appears that authenticatedMovePathTo does not work when trying to move a file out of a write protected bundle?  I cannot move a file out of one of the included Apple applications with this method.  Any ideas?

----

I don't see why people keep inventing new ways to create "unique" temporary file names when UNIX has this functionality built-in for years. Try "man 3 mktemp" in your neighborhood Terminal window.

----

Shouldn't the line
    
char * args[2];

be

    
char * args[3];

??


[Question?]
Undefined symbols for architecture x86_64:
"_AuthorizationCreate", referenced from:
      -[NSFileManager(TRAdditions) authorizedMovePath:toPath:] in NSFileManagerAdditions.o

how to solve this?

