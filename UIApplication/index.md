---
layout: page
title: UIApplication
---



Part of the iPhone General/UIKit framework.

iPhone applications must (?) have a General/UIApplication subclass, which is specified in the UIA<nowiki/>pplicationMain() call.

    
int main(int argc, char **argv)
{
    General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
    return General/UIApplicationMain(argc, argv, General/[SampleApp class]);
}


These callbacks do exactly what they say they do. That's why we love Cocoa.

<code>- (void)applicationDidFinishLaunching:(id)unused;</code>

<code>- (void)applicationWillTerminate;</code>
is to end the application.

No idea what the argument is for..

<code>- (void)applicationWillSuspend;</code>

<code>- (void)deviceOrientationChanged:(General/GSEvent*)event;</code>

Wouldn't this be called when the iPhone is rotated to the horizontal or vertical position? Then call + (int)deviceOrientation:(BOOL)flag; from General/UIHardware to get the current position and update your app's display accordingly?

<code>- (void)applicationResume:(General/GSEvent*)event;</code>

Called when the user selects the icon for a suspended application (also when a call ends?).

<code>- (void)applicationSuspend:(General/GSEvent*)event;</code>

Called when the phone switches back to the Springboard in response to the menu button (also when it switches to a call?). The app can call [self terminate] here if it doesn't need to keep running.

// ???

<code>- (void)menuButtonUp:(General/GSEvent*)event;</code>

<code>- (void)menuButtonDown:(General/GSEvent*)event;</code>

<code>- (BOOL)launchApplicationWithIdentifier:(General/NSString*)identifier suspended:(BOOL)flag;</code>

Launches the specified application.

<code>- (void)openURL:(NSURL*)url;</code>

openURL can be used to open a browser, or to make a call:

    
[self openURL: General/NSURL alloc] initWithString: @"http://google.com"
[self openURL: General/NSURL alloc] initWithString: @"tel://650-555-1234"


<code>- (void)openURL:(NSURL*)url asPanel:(BOOL)flag;</code>

----

General/UIApplicationMain() expects an General/NSString (or nil, if using General/UIApplication), not a class for its third argument. Also, the thing about how iOS apps *must* have a General/UIApplication subclass is wrong - General/UIApplicationDelegate is there to minimize the need for subclassing. Like a lot of classes in Cocoa and Cocoa Touch, just use it as-is. Finally, General/UIApplicationMain() is missing its fourth argument, an General/NSString describing the delegate class name (or nil if specified elsewhere). --lowell
