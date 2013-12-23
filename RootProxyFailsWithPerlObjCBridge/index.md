---
layout: page
title: RootProxyFailsWithPerlObjCBridge
---

I wanted my application to be able to execute Perl scripts that in turn are supposed to communicate with it.
My first thought was using Distributed Objects, so I set up an NSConnection using ObjC, registered it and set a root Object.
Then on the Perl part my approach was supposed to be:
- getting an NSConnection object connected to the first one using the registered name
- retreiving a Proxy to the root object
- send a few test messges

My code however fails at retrieving the root object and I just can't find out why....

This is my Obj-C code:

----
    
#import <Cocoa/Cocoa.h>

BOOL didPerform = NO;

@interface Logger : NSObject
{
}

-(void) logNSString: (NSString *) str;

@end


@interface PerlController : NSObject
{
	NSTask *perl;
	Logger *logger;
}

-(void) loadPerlStuff;
-(void) unloadPerlStuff;

-(Logger *) logger;

-(void) finish;

@end

int main( void )
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	
	NSConnection *con = [[NSConnection alloc] initWithReceivePort: [NSPort port] sendPort: [NSPort port]];
	PerlController *pCntl = [[PerlController alloc] init];
	
	[con setRootObject: pCntl];
	if( [con registerName: @"PerlConnection"] == NO )
	{
		NSLog( @"Could not register connection." );
		[con release];
		
		[pool release];
		return 1;
	}
	
	NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
	[runLoop performSelector: @selector(loadPerlStuff) target: pCntl argument: nil order: 1 modes: [NSArray arrayWithObject: NSDefaultRunLoopMode]];
	
	while( didPerform == NO )
		[runLoop runMode: NSDefaultRunLoopMode beforeDate: [NSDate distantFuture]];
	
	[pCntl unloadPerlStuff];
	[pCntl release];
	
	[con registerName: nil];
	[con release];
	
	[pool release];
    return 0;
}

@implementation Logger
		  
-(void) logNSString: (NSString *) str { NSLog( @"%@", str ); }
		  
@end

@implementation PerlController : NSObject

-(void) loadPerlStuff
{
	NSString *path = @"/usr/bin/perl";
	NSFileHandle *handle = [NSFileHandle fileHandleForReadingAtPath: path];
	if( handle == nil )
	{
		path = @"/usr/local/bin/perl";
		handle = [NSFileHandle fileHandleForReadingAtPath: path];
		if( handle == nil )
		{
			NSLog( @"Could not locate perl" );
			return;
		}
	}
	
	perl = [[NSTask alloc] init];
	
	[perl setArguments: [NSArray arrayWithObject: @"script.pl"]];
	[perl setCurrentDirectoryPath: @"./"];
	[perl setLaunchPath: path];
	
	logger = Logger alloc] init];
	
	[perl launch];
}

-(void) unloadPerlStuff
{
	[perl terminate];
	[perl release];
	
	[logger release];
}

-(Logger *) logger { return logger; }

-(void) finish { didPerform = YES; }

@end


----

This is my Perl code:

----
    

#!/usr/bin/perl

use Foundation;

my $con = [[NSConnection->connectionWithRegisteredName_host_(NSString->stringWithCString_("PerlConnection"), 0);

my $cntl = $con->rootProxy();

if( !$cntl or !$$cntl ) {
	print "damn it....";
	exit(1);
}

$cntl->logger()->logNSString_(NSString->stringWithCString_("Log this!"));
$cntl->finish();


----

