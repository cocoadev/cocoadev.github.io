---
layout: page
title: AppWindowBehaviour
---

Another simple one I'm afraid...

(sorry if these irritate some people... I truly try to find answers elsewhere, but while its dead easy to find tutorials & samples for the key parts of Cocoa, it's the little details that are difficult to discover)

Problem: I want the user to be able to close the main window of my app without my app terminating, but be able to makeKeyAndOrderFront it when they click on the app icon in the dock, bringing the main Window back to where it was before they dismissed it.

This behaviour can be seen in a lot of Apple's apps, Address Book, Mail etc.

The standard app behaviour is what I have at the moment where the user can close the window, app keeps running (so far so good), but clicking on the dock icon does not bring back the app window.

Pointers to achieving the desired behaviour are truly appreciated.

P

----

in your app delegate:

    

- (void)applicationDidBecomeActive:(NSNotification *)aNotification
{
    // show your window here
}



----

Thanks - but this only works if you close the window, go to a different app, then click the icon in the dock. If you simply close the window and then try to get it back straight away it doesn't work (presumably because applicationDidBecomeActive isn't being called - it's already the active app).

Apps like Address Book on the other hand whenever you hit that icon in the dock, the window springs up, whether you've switched away from the app or not.

Help much appreciated!

----

How about returning YES from     -(BOOL)applicationShouldOpenUntitledFile:(NSApplication*)application and, if neccesary, implementing     - (BOOL)applicationOpenUntitledFile:(NSApplication *)theApplication

----

I just wanted to post this information on the end of this page, since I spent some time looking around and stumbled here, and it looks like the correct answer was never found.  To get this functionality, add to your app's delegate something like this:

    

- (BOOL)applicationShouldHandleReopen:(NSApplication *)theApplication
             hasVisibleWindows:(BOOL)flag
{
	if( !flag )
		[mainWindow makeKeyAndOrderFront:nil];
	
	return YES;
}


----
I'll pile on here. It sounds like the behavior the OP wants is to hide the window when the user hits the close button, and show it again when they click the app's icon or otherwise reopen the app. In a nutshell, you'll want to handle the reopen event as above, and also handle the window delegate method -shouldWindowClose:. For a simple app, you might even put both these methods in a single controller that's both the application's delegate and the window's delegate, like this:
    
#import "WindowDelegate.h"

@implementation WindowDelegate
- (BOOL)applicationShouldHandleReopen:(NSApplication *)theApplication
                    hasVisibleWindows:(BOOL)flag
{
    if( !flag )
        [theWindow makeKeyAndOrderFront:self];
    
    return YES;
}    

- (BOOL)windowShouldClose:(id)sender
{
    BOOL result = YES;
    
    if (sender == theWindow) {
        [theWindow orderOut:self];
        result = NO;        
    }
    
    return result;
}
@end

In the preceding code, theWindow is an IBOutlet that's hooked up to the window in question. It's expected that an instance of WindowDelegate is created in the nib file, and that it is the delegate of both the app (i.e. File's Owner in the MainMenu.nib of a plain Cocoa app) and whatever window you want to appear when the app is reopened.

Again, the idea here is to keep the window around rather than actually closing it when the user clicks the close button. But that's just one way to do it... another approach would be to allow the window to close, and then reload the window from its nib again when the user reopens the app.
-CS
----
An easy way to make it so that your main window doesn't completely go away (i.e. get deallocated) when it's closed is to just uncheck the "Release When Closed" checkbox in the properties for it in Interface Builder (or the inline UI editor in Xcode 4).

