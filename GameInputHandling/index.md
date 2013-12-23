---
layout: page
title: GameInputHandling
---

Seems to be at least three ways to handle events in Cocoa games.

1.) Create your own run loop.
2.) Override sendEvent
3.) Use HID code

Can someone critique this example of #2 with multithreaded drawing thrown in. Would it be preferable to just create your own run loop? One of the main reasons I would say that this is preferable to #1 is that finding good examples of creating a run loop are few and far between and it is not recommended in the offical docs of NSApplication that you do so.

--Overridden NSApplication--
    
-(void)sendEvent:(NSEvent *)anEvent {
	switch ([anEvent type]) {
		case NSLeftMouseUp:
		case NSLeftMouseDown:
		case NSRightMouseUp:
		case NSRightMouseDown:
		case NSKeyUp:
		case NSKeyDown: {
			[input queueEvent:anEvent];
			break;
		}
		
		default: {
			[super sendEvent: anEvent];
		}
	}
}


--Input class--
    
-(id)init {
	self = [super init];
	
	if (self != nil) {
		eventQueue = [[NSMutableArray alloc] initWithCapacity:256];
		queueLock = [[NSConditionLock alloc] initWithCondition:NO_DATA];
	}
	
	return self;
}

-(void)dealloc {
	if (eventQueue != nil) {
		[queueLock lock];
		[eventQueue removeAllObjects];
		[eventQueue release];
		[queueLock unlock];
	}
	
	if (queueLock != nil) {
		[queueLock release];
	}
	
	[super dealloc];
}

-(void)queueEvent:(NSEvent *)anEvent {
	[queueLock lock];
	[eventQueue addObject:anEvent];
	[queueLock unlockWithCondition:HAS_DATA];
}

-(NSEvent *)dequeueEventNoWait {
	NSEvent *event = nil;
	
	if ([queueLock tryLockWhenCondition:HAS_DATA]) {
		event = [eventQueue objectAtIndex:0];
		[event retain];
		[eventQueue removeObjectAtIndex:0];
		
		if ([eventQueue count] == 0) {
			[queueLock unlockWithCondition:NO_DATA];
		} else {
			[queueLock unlockWithCondition:HAS_DATA];
		}
	}
	
	return event;
}

-(NSEvent *)dequeueEventWait {
	NSEvent *event = nil;
	
	[queueLock lockWhenCondition:HAS_DATA];
	
	event = [eventQueue objectAtIndex:0];
	[event retain];
	[eventQueue removeObjectAtIndex:0];
	
	if ([eventQueue count] == 0) {
		[queueLock unlockWithCondition:NO_DATA];
	} else {
		[queueLock unlockWithCondition:HAS_DATA];
	}
	
	return event;
}


--Game Loop--
    
-(void)playGame:(id)anObject {
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
	NSDate *frameStart;
	NSDate *frameEnd;
	
	[self loadGame]; // Load resources for game

	while (gameRunning) {
		frameStart = [[NSDate alloc] init];
		frameEnd = [[NSDate alloc] initWithTimeInterval:0.016 sinceDate:frameStart];

		// Wait for events to be posted and act accordingly
		NSEvent *event = [input dequeueEventNoWait];
		
		if (event != nil) {
			switch ([event type]) {
				case NSLeftMouseDown: {
					[self openInGameMenu];
					break;
				}
			
				case NSKeyDown: {
					[NSApp terminate:nil];
					break;
				}
			
				default: {
				}
			}
		
			[event release];
			
			if (!gameRunning) {
				break;
			}
		}
		
		[self drawFrame]; // Draw single frame of animation

		[NSThread sleepUntilDate:frameEnd];
		[frameStart release];
		[frameEnd release];
	}

	if (frameStart != nil) {
		[frameStart release];
	}
	
	if (frameEnd != nil) {
		[frameEnd release];
	}
	
    [pool release];
	
	if (menuActive) {
		[NSThread detachNewThreadSelector:@selector(openMainMenu:) toTarget:self withObject:nil];
	} else {
		NSLog(@"Shouldn't be able to get here");
		[NSApp terminate:nil];
	}
}

