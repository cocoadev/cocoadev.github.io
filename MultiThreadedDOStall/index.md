---
layout: page
title: MultiThreadedDOStall
---



Hello all-

**What I am trying to do:** 
a) run some lengthy operations in the 'background'
b) show a progress indicator to update the progress of the operation
c) make sure that the GUI is still responsive and that the thread can be stopped/restarted

**How I did it:**
a) I used code from the Anguish/Buck/Yacktman book "Cocoa Programming" example 'ThreadExample'
b) uses Distributed Objects to setup additional threads that allow for talking between the multi-thread and the 'main' thread

**some code:**
Controller class-
    
@protocol ApplescriptController
// the server object will send the controller (parent) these messages
- (void)setServer:(id)anObject tag:(int)serverTag;
- (void)addData:(id)data tag:(int)tag;	// indicates that the process running in the serverobject is returning data
- (void)sendError:(NSDictionary *)dict;
- (void)processFinished:(int)tag;

- (void)launchThread;

// intended for progress displays
- (void)startProcess;
- (void)stopProcess;

@interface iLHThreadediTunesController : NSObject <ApplescriptController>  {
	id <ApplescriptMethods>setInterface;
}
@end

@implementation iLHThreadediTunesController
- (id)init {
    self = [super init];
    if (self) {
		setInterface=nil;
	}
    return self;
}
- (void)launchThread {
	[iLHThreadedSetApplescriptInterface startServerThreadWithTag:0 forController:self];
}
- (void)setServer:(id)anObject tag:(int)serverTag {
	[anObject setProtocolForProxy:@protocol(ApplescriptMethods)];
	[anObject retain];
	if(serverTag==0) setInterface=(id <ApplescriptMethods>)anObject;
}
- (void)addData:(id)data tag:(int)tag {
	if(tag==0) {	// coming from interface, data= NSDictionary
		// just setting data for display here
	}
}

server object class-
    
@protocol ApplescriptMethods
// the applescript controller (parent) calls these to control the applescript object
- (void)start;
- (void)togglePause;
- (void)pause;
- (void)resume;
- (void)stop;
- (void)exit;
@end

@interface ApplescriptObject : NSObject <ApplescriptMethods> {
    int amountDone, tag;
    BOOL paused, running, suppressProgress;
    id <ApplescriptController> parent;
}
// you call this to spawn a new thread
+ (NSConnection *)startServerThreadWithTag:(int)tag forController:(id <ApplescriptController>)controller;
// these are for subclasses to override
- (id)initForParent:(id <ApplescriptController>)theParent withTag:(int)theTag; // be sure to call super!
@end

@interface ApplescriptObject(_private)
// main thread calls this to set up a new server; called by method above
// this runs in the new thread.
+ (void)_connectWithPorts:(NSArray *)portArray;
@end


@implementation ApplescriptObject
// sets up a DO connection in the main thread and then kicks off a
// new thread with a brand new server object in it.
+ (NSConnection *)startServerThreadWithTag:(int)theTag forController:(id <ApplescriptController>)controller {
    NSPort *port1 = [NSPort port];
    NSPort *port2 = [NSPort port];
    NSArray *portArray = [NSArray arrayWithObjects:port2, port1, [NSNumber numberWithInt:theTag], nil];
    NSConnection *serverConnection = [[NSConnection alloc] initWithReceivePort:port1 sendPort:port2];
    [serverConnection setRootObject:controller];
    [NSThread detachNewThreadSelector:@selector(_connectWithPorts:) toTarget:self withObject:portArray];
    return serverConnection;
}

// finish setting the the DO connection to the main thread
// and set up a run loop for this thread.  The main thread
// will call us with requests to process data which the run
// loop will pick up.  If there's nothing to do, the run
// loop will block and no CPU time will be wasted.
+ (void)_connectWithPorts:(NSArray *)portArray {
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    NSConnection *serverConnection = [NSConnection connectionWithReceivePort:[portArray objectAtIndex:0] sendPort:[portArray objectAtIndex:1]];
    int theTag = portArray objectAtIndex:2] intValue];
    id rootProxy = (id)[serverConnection rootProxy];
    [[ApplescriptObject *serverObject = self alloc] initForParent:rootProxy withTag:theTag];
    [rootProxy setServer:serverObject tag:theTag];
    [serverObject release];
    
	[[[[NSRunLoop currentRunLoop] run];
    [pool release];
}

- (id)init {
    return [self initForParent:nil withTag:0];
}
- (id)initForParent:(id <ApplescriptController>)theParent withTag:(int)theTag {
    self = [super init];
    if (self) {
		tag = theTag;
		amountDone = 0;
		parent = theParent;
		suppressProgress = NO;
		running = NO;
		paused = NO;
	}
    return self;
}
- (void)start {
    [self stop];
    amountDone = 0;
    running = YES;
}
- (void)togglePause {
    if (!running) return;
    if (paused) [self resume];
    else [self pause];
}
- (void)pause {
    paused = YES;
}
- (void)resume {
    if (!running) return;
    paused = NO;
}
- (void)stop {
    running = NO;
}
- (void)exit {
}
- (BOOL)isRunning {
    return running;
}
- (BOOL)isPaused {
    return paused;
}

server object subclass
    
@interface iLHThreadedSetApplescriptInterface : ApplescriptObject {
}
@end

@implementation iLHThreadedSetApplescriptInterface
// overridden so that we suppress progress by default
- (id)initForParent:(id)theParent withTag:(int)theTag {
    self = [super initForParent:theParent withTag:theTag];
    if (self) {
	}
    return self;
}
-(void)dealloc {
	[super dealloc];
}

// ******' this is the operation that is stalling the GUI !!! ******' //
// all it is doing is taking a bunch of images and creating a pdf from them
- (void)setBooklet:(id)data; {	// data=array of data objects
	[parent startProcess];
	// create digital booklet and send to iTunes
	// 1) create an images array from the data array
	NSMutableArray *imagesArray=[NSMutableArray arrayWithCapacity:0];
	int z;
	for(z=0;z<[data count];z++) {
		NSData *nextObject=[data objectAtIndex:z];
		NSImage *nextImage=[[[NSImage alloc] initWithData:nextObject] autorelease];
		if(nextImage!=nil) {
			[imagesArray addObject:nextImage];
		}
	}
	
	// 2) save pdf to file
	NSString *pathToFile=[NSString stringWithFormat:@"%@/booklet.pdf",pathToFolder,nil];
	iLHBookletImageView *bookletPDF=iLHBookletImageView alloc] 
										initWithFrame:[[NSMakeRect(0.0f,0.0f,800,600)
										images:imagesArray];
	
	// print as a pdf- save to file
	NSDictionary *printInfoDict=[[NSDictionary alloc] initWithObjectsAndKeys:[pathToFile stringByExpandingTildeInPath],NSPrintSavePath,nil];
	NSPrintInfo *printInfo=[[NSPrintInfo alloc] initWithDictionary:printInfoDict];
	[printInfo setJobDisposition:NSPrintSaveJob];
	[printInfo setTopMargin:0.0f];
	[printInfo setBottomMargin:0.0f];
	[printInfo setLeftMargin:0.0f];
	[printInfo setRightMargin:0.0f];
	// get the page size from the images saved and set the paper size
	[printInfo setPaperSize:[bookletPDF getPageSizeFromImages]];
	[printInfo setVerticallyCentered:YES];
	[printInfo setHorizontallyCentered:YES];
	id op=[NSPrintOperation printOperationWithView:bookletPDF printInfo:printInfo];
	[op setShowPanels:NO];
	[op runOperation];
	
	[printInfoDict release];
	[printInfo release];
	[bookletPDF release];
	[parent stopProcess];
}


**What is happening:**
a) the threads are started up properly (I checked with Thread Viewer)
b) the processes are running in the background thread (again, checked with Thread Viewer)
c) but, the GUI is stalled and the pin wheel shows up.  By using Spin Control, I verified that it is the background thread that is causing the problem

Can anyone verify that I am doing this properly?  Should my GUI be stalled?  Is there a better way to do this?

Thanks in advance

- Ryan

----
You are going to have to tell us more.  I just downloaded and built the example from "Cocoa Programming" and it worked great.  The GUI was responsive all the time.  However, I can't help thinking it could be a much simpler example now that Apple has given us 
�  performSelectorOnMainThread:withObject:waitUntilDone:  
�  performSelectorOnMainThread:withObject:waitUntilDone:modes:  

Just update the progress bar in a method that you execute via -performSelectorOnMainThread:withObject:waitUntilDone: called from the worker thread.  The worker thread can check the value of a shared variable periodically to see if the work should be canceled or not.
----

I just spotted the problem.  The AppKit is not thread safe, and you are using the AppKit from the main thread and the worker thread.  You can't use NSImage or NSPrintInfo or iLHBookletImageView (assuming is is a subclass of NSView) in your worker thread!
----

Why are you doing this is a sepearte THREAD ?  Why not use an entirely seperate server application if you are going to use DO and/or AppleScript anyway ?
A server application will have its own connection to Quartz and its own protected memory etc.  

*Multi-threaded programming is an insidious nightmare and should be avoided whenever possible.*

See also SignalSafety, NotificationsAcrossThreads, UpdatingUIWhileBusyWithoutThreads, ThreadSafety, UpdatingDisplayDuringTimeConsumingTasks, NSTaskTermination, NSProgressIndicatorInMultithreadedDrawing, ProducersAndConsumerModel, 
----

I recommend the TrivialThreads sample from developer.apple.com as a better (more current) sample from to base your project.
BroadcasterDaemon is another relevant Apple sample.

----

Thanks for the response eveyone!  As always, this site provides quick and useful responses.  I have a lot to chew on here so I will post my solution when I come up with one...

-Ryan

----
I wouldn't go so far as to say MT is a nightmare and should be avoided. However, I would not recommend Cocoa MT for beginners. It is the most complicated threading API to use that I have seen. Yes, I admit that methods such as performSelectorOnMainThread make it very easy to create an run threads. Communication between threads is very difficult in Cocoa. It is difficult because it isn't well understood - Proxies, Connection Kits, run loops, more proxies, arg!. I don't understand it that well and I am not going to release MT code that I don't understand. 

I suggest looking at some older and simpler MT APIs like pthreads. You can set up a simple producer-consumer model with a mutex and a container. It will run fast, be simple to code, and fairly foolproof. 

----
Just to make it clear, you can set up a simple ProducersAndConsumerModel with an NSConditionLock and an NSArray — no need to delve into pthreads unless you want to. Also, the WorkerThread page is an attempt to provide a simple model for multithreading, again 100% Cocoa-based.

