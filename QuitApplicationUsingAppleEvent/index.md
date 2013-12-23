---
layout: page
title: QuitApplicationUsingAppleEvent
---

Code to quit an application, identified by a bundle ID, by sending it a Quit Apple Event.

Sending a Quit Apple Event is the "friendly" way to quit an application.  In response, an application may display some UI to the user such as asking him to save unsaved documents.  This code executes much faster than using either NSAppleScript or NSTask to quit an application.  It requires 10.3 or later.

    
-(OSStatus)quitApplicationWithBundleID:(NSString *)bundleID {
    OSStatus err;
    AppleEvent event, reply;

    const char *bundleIDString = [bundleID UTF8String];

    err = AEBuildAppleEvent(kCoreEventClass, kAEQuitApplication,
                            typeApplicationBundleID, 
                            bundleIDString, strlen(bundleIDString),
                            kAutoGenerateReturnID, kAnyTransactionID,
                            &event, NULL, "");

    if (err == noErr) {
        err = AESendMessage(&event, &reply, kAENoReply, kAEDefaultTimeout);
        (void)AEDisposeDesc(&event);
    }
    return err;
}


If you're actually interested in whether you successfully quit the app or not, change the kAENoReply flag to kAEWaitReply, set a saner timeout (it's in ticks, 1 tick = 1/60 second), and check if the return value is noErr or not.

If you want to force kill a process without the user having any chance to save his work, you can use the Carbon KillProcess() or POSIX kill() APIs.

Lastly, note that it is possible for there to be more than one application running with the same bundle id.  I'm not sure what happens then.

-- Please keep this article encyclopedic not in MailingListMode.

In the interests of completeness here is a new implementation which uses 10.6 or later APIs:

    
-(BOOL)quitApplicationWithBundleIdentifier:(NSString *)bundleID
{
	BOOL	result = NO;

	for ( NSRunningApplication* app in [[NSWorkspace sharedWorkspace] runningApplications] ) {
		if ( [bundleID isEqualToString:[app bundleIdentifier]] == YES ) {
			[app terminate];
			result = YES;
		}
	}
	
	return( result );
}


Returns YES if the application was found and a quit message was sent. This doesn't actually guarantee that the application has actually quit though.

