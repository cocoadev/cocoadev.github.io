---
layout: page
title: HowToGrabVideoInFoundationTool
---



Hello.

I like to grab a single frame from the iSight-cameras video stream.
It was easy to realize that in an "Cocoa Application". I found Tim's CocoaSequenceGrabber here:
http://www.skyfell.org/cocoasequencegrabber.html

In my Cocoa-Programme the Code looks like this:
    
- (void)awakeFromNib;
{
	// Start recording
	counter = 1;
	camera = [[CSGCamera alloc] init];
	[camera setDelegate:self];
	[camera startWithSize:NSMakeSize(640, 480)];	
}

// CSGCamera delegate

- (void)camera:(CSGCamera *)aCamera didReceiveFrame:(CSGImage *)aFrame;
{
	counter ++;
	if (counter == 10)
	{
		aFrame [[TIFFRepresentation] writeToFile:@"/tmp/foo.tif" atomically:YES];
	       [camera stop];
	       exit(0);
	}
}



Important to know is, that there is a Function "CSGCameraSGDataProc" that takes all the Data from the Component.

    
// Set data proc
    theErr = SGSetDataProc(component, NewSGDataUPP(&CSGCameraSGDataProc), (long)self);
    if (theErr != noErr) {
        NSLog(@"SGSetDataProc() returned %ld", theErr);
        return NO;
    }


In a Cocoa-Application this works great. The Data is passed from function to function and each frame is processed by 
"- (void)camera:(CSGCamera *)aCamera didReceiveFrame:(CSGImage *)aFrame;", where i can pick it and write it into a file.

Now i want to have this in a "Foundation Tool", a command line utility. But there no data is processed. I believe this is because a "Foundation Tool" is not multithreaded by default. I did some experiments with threads, but this does not work. My (maybe stupid) idea now is, it should be possible to create some threaded environment like in a Cocoa-App. Maybe to run the whole class inside a thread.

Can someone give me some hint how to solve this problem ?

Benjamin from Wiesbaden, Germany

----

It's not because a foundation tool is unable to detach threads (NSThread is a foundation class). It could be because some part of the sequence grabbing code is dependent on the window server. Also, the sequence grabbing code could require the QuickTime toolkit to be initialized (see     EnterMovies()). If your code works as a Cocoa application and you only want to avoid the user interface part then check out CocoaAppsWithoutMenuBarOrDock. 

--zootbobbalu

----

Thanks for that help "zootbobbalu". 
I analysed my problem some more hours.

 I thinks its because in a Foundation Tool, i do not have an "Event Cycle", as its shown here:

http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/Art/main_event_loop.gif

on http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CoreAppArchitecture/chapter_7_section_2.html#//apple_ref/doc/uid/TP40002974-CH8-DontLinkElementID_117

So, my Question is now: How do i enable an Event Cycle in a Foundation Tool.

This
    
void NSApplicationMain(int argc, char *argv[]) {
    [NSApplication sharedApplication];
    [NSBundle loadNibNamed:@"myMain" owner:NSApp];
    [NSApp run];
}
 
would start a program with windows and stuff as defined in a nib-File. But this needs a connection to a Window-Server. 
But my application needs to be independent from the Window Server, but it needs the ability to delegate to functions in an event cycle.

Any ideas ?

Ben

----
----
You can't grab video without a connection to the window server. Like many things in OS X which are centralized but don't have an immediate obvious relationship to drawing windows on the screen, video capture goes through the window server. If you absolutely need a tool which has no window server connection, split your program into two processes, one permanent one which has no such connection, and a subprocess which is executed just for video capture.

----

Can you explain why grabbing video without UI elements (CocoaAppsWithoutMenuBarOrDock ) is not an option? Are you trying to do this as a background process and don't know how to launch this type of application? Can you explain in more detail why the tool must be independent from the Window Server? --zootbobbalu

----

Hey zootbobbalu.

CocoaAppsWithoutMenuBarOrDock ist great - but I want to grab pictures as a procedure of a daemon-based project I write at the moment. This daemon is started at with MacOS X when there is no Window Server and I do not want it to rely on the window server. But I have the feeling i can't avoidance it. Thanks for your help!

----

I did some more experiments. I created an cocoa application which had an extra thread that listens to a socket (some kind of server). As long as this thread runs, no videoframe is captured. I first thought I somehow killed the delegations, but i found out, that the event cycle seems stopped while the thread (which included an endless while loop) was running. As soon as the thread ended, capturing worked.
Assumed that this is only a question of the event cycle (I do not believe that its necessary to have the window server for frame grabbing from the iSight) I think about playing with the RunLoop feature. ... I will tell you later if I was successful. -Ben

----

There are two iSight Unix tools called grabframes & isightcapture:

http://osx.hyperjeff.net/Apps/apps.php?f=iSight

----

OK, I found a solution to build it as a Foundation Tool. It was really just a question of the NSRunLoop. There is no dependency to the window server. 

    
#import <Foundation/Foundation.h>
#import "mainApplication.h"

int main (int argc, const char * argv[]) {
	NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	
	NS_DURING
	{
		mainApplication *thisApplication = [mainApplication alloc] init] autorelease];
		[thisApplication startUp];
		
		// Run runloop
		[[[[NSRunLoop currentRunLoop] run];
	}
	NS_HANDLER
		NSLog(@"...");
	NS_ENDHANDLER
	[pool release];
	return 0;
}


You can download a universal binary here: http://benjamindahlhoff.wordpress.com/2006/10/02/isight-image-grabber-for-the-mac-os-x-command-line/
- Ben

