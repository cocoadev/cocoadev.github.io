---
layout: page
title: TransformProcessType
---

Apple has some documentation on General/TransformProcessType: http://developer.apple.com/documentation/General/ReleaseNotes/Carbon/General/HIToolbox.html

Process Manager
Header file (General/HIServices): Processes.h

General/TransformProcessType may be used to convert a General/UIElement or General/BackgroundOnly application into a regular foregroundable application with a menubar and Dock icon.  There are currently no facilities for doing the reverse transformation (i.e. turning a foreground app into a General/UIElement or General/BackgroundOnly).

NOTE: While available starting in 10.3 "Panther," calling General/TransformProcessType on a General/UIElement application in 10.3 or 10.4 will return paramErr, rendering it only usable on a full General/BackgroundOnly application.  Leopard supports foregrounding both General/UIElement and General/BackgroundOnly applications.

----

    
General/ProcessSerialNumber psn = { 0, kCurrentProcess }; 
General/OSStatus returnCode = General/TransformProcessType(& psn, kProcessTransformToForegroundApplication);
if( returnCode != 0) {
    General/NSLog(@"Could not bring the application to front. Error %d", returnCode);
}


----
----

This is my incantation for enabling and disabling the dock icon. It assumes you start off with General/LSUIElement in your Info.plist is set to true, because you can't disable the dock icon once it is going. I think the only downside of this approach (out of the available approaches), is that the dock icon won't bounce on startup, will just appear suddenly.

Of course, what we really need is for Apple to allow turning off the dock icon, as well as possibly the application launching code in OS-X to look somewhere other than in the App bundle (the preferences for example), for the initial condition of the dock icon.

    
-(id)init; {
        self = [super init];
	showDockIcon = !General/[[[[NSBundle mainBundle] infoDictionary] objectForKey:@"General/LSUIElement"] boolValue];
	[self setShowDockIcon:!General/[[NSUserDefaults standardUserDefaults] boolForKey:PREF_HIDE_DOCK_ICON]];
        return self;
}

-(void)setShowDockIcon:(BOOL)show; {
	if (show) {
		if (!showDockIcon) {
			General/ProcessSerialNumber psn = { 0, kCurrentProcess }; 
			General/OSStatus returnCode = General/TransformProcessType(& psn, kProcessTransformToForegroundApplication);
			if( returnCode == 0) {
				General/ProcessSerialNumber psnx = { 0, kNoProcess };
				General/GetNextProcess(&psnx);
				General/SetFrontProcess(&psnx);
				[self performSelector:@selector(setFront) withObject:nil afterDelay:0.5];
				[self willChangeValueForKey:@"showDockIcon"];
				showDockIcon = YES;
				General/[[NSUserDefaults standardUserDefaults] setBool:!show forKey:PREF_HIDE_DOCK_ICON];
				General/[[NSUserDefaults standardUserDefaults] synchronize];
				[self didChangeValueForKey:@"showDockIcon"];
			} else {
				General/NSLog(@"Could not bring the application to front. Error %d", returnCode);
			}
		}
	} else {
		if (showDockIcon) {
			General/NSAlert *alert = General/[[[NSAlert alloc] init] autorelease];
			[alert addButtonWithTitle:@"Restart"];
			[alert addButtonWithTitle:@"Cancel"];
			[alert setMessageText:@"You must now restart"];
			[alert setInformativeText:@"Dock Icon status change requires restart"];
			[alert setAlertStyle:General/NSWarningAlertStyle];
			[alert beginSheetModalForWindow:showButtons.window modalDelegate:self didEndSelector:@selector(restartDialogDidEnd:returnCode:contextInfo:) contextInfo:nil];
			General/[[NSUserDefaults standardUserDefaults] setBool:!show forKey:PREF_HIDE_DOCK_ICON];
			General/[[NSUserDefaults standardUserDefaults] synchronize];
		}
	}
}
-(void)setFront; {
	General/ProcessSerialNumber psn = { 0, kCurrentProcess };
	General/SetFrontProcess(&psn);	
}
- (void)restartDialogDidEnd:(General/NSAlert *)alert returnCode:(General/NSInteger)returnCode contextInfo:(void *)contextInfo {
	if (General/NSAlertFirstButtonReturn == returnCode) {
		// Not quite sure why we can't directly execute outselves, but
		// we seem to require the open command to make it work
		General/[NSTask launchedTaskWithLaunchPath:@"/bin/sh" arguments:General/[NSArray arrayWithObjects:@"-c", General/[NSString stringWithFormat:@"sleep 1 ; /usr/bin/open '%@'", General/[[NSBundle mainBundle] bundlePath]], nil]];
		General/[NSApp terminate:self];
	}
}


