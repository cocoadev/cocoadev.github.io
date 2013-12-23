---
layout: page
title: KeyDownEvent
---






----
**See KeyboardEventsFromOtherApps**



----
**Old Discussion:**
----


I'd like to be able to capture all incoming key events - as in, my process becomes the only one to receive them. I worked out the following bit of code to do the first part of this task - it should run the <code>keyPressed()</code> function whenever a key is pressed.

    #include <Carbon/Carbon.h>

OSStatus keyPressed(EventHandlerCallRef nextHandler, EventRef theEvent, void *userData) {
	NSLog(@"Event received!\n");
	return CallNextEventHandler(nextHandler, theEvent);
}

@implementation EventHandler

- (id)init {
	EventTypeSpec eventType;
	eventType.eventClass = kEventClassKeyboard;
	eventType.eventKind = kEventRawKeyDown;
    
	EventHandlerUPP handlerFunction = NewEventHandlerUPP(keyPressed);
	InstallEventHandler(GetEventMonitorTarget(), handlerFunction, 1,
                                                  &eventType, NULL, NULL);
	
	return self;
}

@end

Of course, it doesn't work for me - whenever a key is hit I hear a beep, but <code>keyPressed()</code> is never accessed. A quick check shows that <code>InstallEventHandler()</code> is successful, and none of the variables are invalid.

I would gladly use NSEvent<nowiki/>s and such, as suggested by a few other topics here, but I am fairly lost on how to set something up which would accomplish this task. It seems more involved, but I'll go for it (as long as it works!).

-- RyanGovostes

----

Are you trying to capture key events even when your app is not frontmost? Key events go to the frontmost app, period. An exception is made for the global hotkeys API, but that's it. There is no easy way to do it in the general case.

----

According to CarbonEvents.h, GetEventMonitorTarget() receives all events of the specified class and type, as long as your program isn't frontmost. I'm not sure whether mine is or not, since it is a background application (<code>LSUIElement</code> is set to <code>1</code> in the Info.plist), and the only window is an overlay window.

-- RyanGovostes

----

I was unaware of that and I didn't notice it in your code, that is very interesting. Did you see the part in the header that says:

*For added security, GetEventMonitorTarget requires that "Enable
access for assistive devices" be checked in the Universal Access
preference pane in order to monitor RawKeyDown, RawKeyUp, and
RawKeyRepeat events. If this control is not checked, you can
still install handlers for these events on the event monitor
target, but no events of these types will be sent to your
handler. Administrator privileges are required to enable this
feature.*

It sounds like this might be your problem. If that makes it work, please report back, I'm interested to know if it can be done.

----

I have "Enable access for assistive devices" turned on, for UI scripting with AppleScript - so no, turning this on doesn't affect the code at all. I'd rather my program didn't require this, but it's not a huge deal if it does. Would there be any benefits of using the Cocoa way over the Carbon Event Manager? -- RyanGovostes

----

I worked around this by using a hidden NSTextView which sends data to my EventHandler class after the data is finished. On one hand, I'm glad Apple made this nearly impossible to prevent keyloggers (though there are still a few), but on the other it is annoying there is no way to do this for other purposes. Security is a double-edged blade, or something. Bleh. -- RyanGovostes

----

I am facing the same problem described above.  The EventMonitorTest code that was recently posted on Apple's development site provides a working example (although, key strokes require the assistive device enabling).  However, I attempted to wrap the code within an Obj-C class, and the keystroke functionality did not work (bell sounds on all keystrokes, but the callback was not triggered).  Mouse events work.  I'm really perplexed by this, as my changes from the Apple sample code were minimal.  --KarlGyllstrom

----

Investigate the HID sample code on Apples site, there used to be an example that monitored a HID queue that just happened to be the keyboard buffer, though you would have to do a bit of decoding to work out the real key presses... Describe what you are really trying to do (and why), and I may remember more details.  -- RbrtPntn

----

Using the code snippit above i *was* able to get something working.  In the process of messing around with alternative ways of doing this I started using a custom NSApplication subclass to trap all keyup events for my window.  When i tested again with this sample code the callback passed to the InstallEventHandler function was still not getting called but my regular event handler was.  The bell sound that KarlGyllstrom mentions is due to the fact that the window is interpreting these the same way it would if the application had focus with no controls and you were pushing the keys.  Here is the code i got working. --JWMartin

    
#import <Cocoa/Cocoa.h>
#import <Carbon/Carbon.h>

OSStatus keyPressed(EventHandlerCallRef nextHandler, EventRef theEvent, void *userData) {
       NSLog(@"Event received!\n");
       return CallNextEventHandler(nextHandler, theEvent);
}

@interface KeyLoggerApplication : NSApplication
{
}
@end

@interface KeyLoggerController : NSObject
{
       EventHotKeyRef reference;
}
- (IBAction)attach:(id)sender;
@end

@implementation KeyLoggerController


- (IBAction)attach:(id)sender
{
       EventTypeSpec eventType;
       eventType.eventClass = kEventClassKeyboard;
       eventType.eventKind = kEventRawKeyUp;

       EventHandlerUPP handlerFunction = NewEventHandlerUPP(keyPressed);
       InstallEventHandler(GetEventMonitorTarget(), handlerFunction, 1, &eventType, NULL, NULL);
}
@end

@implementation KeyLoggerApplication

- (void)sendEvent:(NSEvent *)anEvent {

       NSEventType type = [anEvent type];
       bool handled = NO;

       if (type == NSKeyUp)
       {
               switch( [anEvent keyCode] )
               {
                       case 36: //return
                               NSLog(@"Pressed return");
                               handled = YES;
                               break;

                       default:
                               NSLog(@"Keypressed: %d, **%@**", [anEvent keyCode], [anEvent characters]);
                               break;
               }
       }

       //handle only the keys i need then let the other go through the regular channels
       //this stops the annoying beep
       if( !handled )
               [super sendEvent:anEvent];
}

@end

----
*
The EventMonitorTest code that was recently posted on Apple's development site provides a working example (although, key strokes require the assistive device enabling). However, I attempted to wrap the code within an Obj-C class, and the keystroke functionality did not work (bell sounds on all keystrokes, but the callback was not triggered). Mouse events work.*

This is driving me crazy. Every example I've found online of using GetEventMonitorTarget() has this exact same problem. Carbon applications work fine, but if you try and incorporate
the same code into a Cocoa app only the Mouse events work, while keystroke events just trigger beeps. Has anybody come up with a solution to this? I really don't want to have to poll with the iGetKeys example...

----
If you want to capture key events in your own app, you will probably have to do it in the Cocoa way (override NSApp sendEvent: or so), and take two separate paths for in-app and out-of-app.

----
Let's say I don't even care about in-app events for now. I just want to be able to capture out-of-app key presses. Why would I need to override NSApp sendEvent: ? Why is [NSApp sendEvent:] even being called in my app when I'm sending keystrokes with different applications in the foreground? It makes no sense to me... hopefully someone here can shed some light.

----

What I meant was that you need [NSApp sendEvent:] *for events in your own app*, in other words when your app is frontmost.

----
Hmmm... thanks for the responses... I've got the feeling at this point that we're just mis-communicating. I understand that I need [NSApp sendEvent:] for keystrokes to trigger actions when my app is in the foreground. But if my app is NOT in the foreground, and I still want to trigger actions within it using keystrokes, should I have to use sendEvent even if I've called GetEventMonitorTarget() ?

----
Follow the code snippit i posted above.  It will allow you to capture keypresses in your application when it is NOT in the foreground.  The event handler you create using GetEventMonitorTarget function will NEVER get called unless you are running a carbon event loop.  The workaround i found was by subclassing NSApplication after installing the event handler using GetEventMonitorTarget all keypresses (even when your app is not in forground) come in via the send event method of the NSApplication.  The beeping noise is because the default implementation is attempting to send this keypress to the control that has focus in the application.  If you follow the code snippit i have posted above you can do exactly what you are trying.  -- JWMartin

----
Josh, any chance you could provide a link to a simple working .xcodeproj version of the snippit you provided?  I tried to work it into a project and am getting build errors.

