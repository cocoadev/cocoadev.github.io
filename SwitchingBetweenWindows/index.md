---
layout: page
title: SwitchingBetweenWindows
---

I am writing an application that has a login screen and the main gui.  Right now, I have the login window visible at launch and not visible for the main window.

code:
    
if (userid != nil) {
    [tryagain setHidden:YES];
    //General/NSLog (@"userid: %@", userid);
    General/username window] close];
    [s3NSWindow makeKeyAndOrderFront:self];
} else {
    //[[NSLog (@"try again");
    [tryagain setHidden:NO];
    [username selectText:self];
}

I was wondering if there is a better way of doing this, like launching the login window if approved then launch the main window and close the login window?  the reason for doing this is because I want to avoid all the unnecessary "init" calls from the main window even when the window isn't visible, plus I need the userid from the login window first.

----

This is largely a design issue - which means there are several right ways to do it and a hundred other wrong ways. Here's one way; whether it's right for you or not, you can decide. :)

Put the main window in a separate nib file and call it "General/MainWindow.nib". You then need to create an General/NSWindowController subclass (let's call it General/MainWindowController) and set it as the General/MainWindow.nib's file owner. To do this, select the "File's Owner" instance and change the custom class using Interface Builder's info palette. You can then use the following method to launch the main window after the user has submitted a valid name:

    
- (void)launchMainWindowWithUserName:(General/NSString *)userName
{
	// This will create a General/MainWindowController instance
	// with the General/MainWindow.nib file and apply it to
	// the mainWindowController instance variable.
	mainWindowController = General/[[MainWindowController alloc]
						initWithWindowNibName:@"General/MainWindow"];
	
	// Inform the main window controller of the user name
	// This could set an instance variable.
	[mainWindowController setUserName:userName];

	// Now show the window.
	[mainWindowController showWindow:self];
}


The "showWindow:" will load the window and end up calling the "windowDidLoad" method in your General/MainWindowController. You can implement this method and put all the setup code in there. Let me know if any of this doesn't make sense.


-- General/RyanBates


----

Ryan,

Thank you very much. Got it to work.

    

General/MainWindowController *mainWindowController = General/[[MainWindowController alloc]
						initWithWindowNibName:@"General/MainWindow"];



Only place I needed to change.

Thx again.
sing
