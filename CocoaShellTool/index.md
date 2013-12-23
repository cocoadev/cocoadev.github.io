---
layout: page
title: CocoaShellTool
---

Xcode will allow you to create a Cocoa target of type "Shell Tool" (Project->New Target->Cocoa->Shell Tool). A Cocoa shell tool has access to the AppKit API after calling     NSApplicationLoad(). This is useful if you would like to write tiny helper apps that don't need workspace support. A simple app created this way can launch very fast. 

    
#import <Cocoa/Cocoa.h>

int main(int argc, char *argv[]) {
	
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
	NSDate *date = [NSDate date];
    
	NSApplicationLoad();
	NSImage *buffer = [[NSImage alloc] initWithSize:NSMakeSize(100.0f, 100.0f)];
	[buffer lockFocus];
	[[NSColor greenColor] set]; 
        NSRectFill(NSMakeRect(0.0f, 0.0f, 100.0f, 100.0f));
	[buffer unlockFocus];
	NSData *tiff = [buffer TIFFRepresentation];
	NSString *file = [NSHomeDirectory() stringByAppendingPathComponent:@"Documents/green.tiff"];
	[tiff writeToFile:file atomically:YES];
	[buffer release];
	
	NSLog(@"duration %f", [[NSDate date] timeIntervalSinceDate:date]);
	[pool release];
	
    return 0;
}

