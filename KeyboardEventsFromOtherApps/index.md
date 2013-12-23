---
layout: page
title: KeyboardEventsFromOtherApps
---

----
**Abstract**

----
To be sent keyboard events destined for other applications, you can use a Carbon event handler targeting the "event monitor" target. There a few 'gotchas' which make this unique in Cocoa.


----
**Requirements**

----
The only requirement for this to work is that "Enable access for assistive devices" is turned on in "Universal Access" when monitoring keyboard events in other apps.


----
**Carbon Code**

----
Simple Carbon code for this can be found in Apple's EventMonitorTest sample code project.


----
**Cocoa Code**

----
Mac OS X 10.4 does some trickery behind the scenes to make the key down and key up events get sent to NSApplication -sendEvent: rather than the event handler. So in this case, you'll need a subclass of NSApplication to handle that. In Mac OS X 10.5, this no longer happens. Instead, you'll receive all events through the event handler as you would in a Carbon application.

For 10.4, the following code applies:

    
//
//  MyApplication.h
// 

#import <Cocoa/Cocoa.h>
#import <Carbon/Carbon.h>

@interface MyApplication : NSApplication {
    EventHandlerRef mMonitorHandlerRef;
}

- (void)installMonitorHandler;
- (void)uninstallMonitorHandler;

@end


//
//  MyApplication.h
// 

#import "MyApplication.h"



@implementation MyApplication

- (void)installMonitorHandler;
{
	OSStatus error = noErr;
	EventTypeSpec	kEvents[] =
	{
		{ kEventClassKeyboard, kEventRawKeyDown },
		{ kEventClassKeyboard, kEventRawKeyUp }
	};
	
	// the magic happens here with the call to GetEventMonitorTarget as described in CarbonEvents.h
	// The event monitor target is a special event target used to monitor user input events across all processes.
	// When the monitor target detects a matching event, then MonitorHandler is called.
	error = InstallEventHandler( GetEventMonitorTarget(), MonitorHandler, GetEventTypeCount( kEvents ),
						 kEvents, NULL, &mMonitorHandlerRef );
	if (error != noErr) {
		NSBeep();
		NSLog(@"InstallEventHandler error %d", error);
	}
}


- (void)uninstallMonitorHandler;
{
	if (mMonitorHandlerRef) {
		RemoveEventHandler(mMonitorHandlerRef);
		mMonitorHandlerRef = NULL;
	}
}


- (void)sendEvent:(NSEvent *)event;
{
	NSEventType eventType = [event type];
	
	// Catch keyboard events destined for other apps when recording
	if (![self isActive] && (eventType == NSKeyDown || eventType == NSKeyUp || eventType == NSFlagsChanged)) {
		NSLog(@"sendEvent: interior");
		return;
	}

    [super sendEvent:event];
}


static OSStatus MonitorHandler(EventHandlerCallRef nextHandler, EventRef theEvent, void * inRefcon)
{
	// Useless on 10.4 for keyboard events. If you requested to watch other events
	// (mouse or tablet events for example) they would into funnel to here.
	return CallNextEventHandler(nextHandler, theEvent);
}

@end





----
**Related Topics**

----
[Topic]

