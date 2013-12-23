---
layout: page
title: SFAuthorizationAuthorizeMethod
---


I hope I'm doing this correctly, I haven't posted here before.  I already flubbed the page name (should have been SFAuthorizationVewAuthorizeMethod).

I have a simple program that implements a SFAuthorizationView to handle authorization.  Everything works great, except I'd like to automatically trigger an authorization (and consequent authentication) attempt and it's not working.

My program edits a launchd daemon plist in /Library/LaunchDaemons/ which is why I'm doing this at all.  When there is no existing plist I'm using a default file within the bundle.  (It occurs to me now that that may be a security concern.. hm).  That concern aside, my call to the authorize: method doesn't seem to do anything.  I'm making other calls to deauthorize: with no problems.

In awakeFromNib: I'm setting up the SFAuthorizationView (called 'authView') like so:

    
	// Set up the Authorization View
	[authView setString:"system.privilege.admin"];
	[authView updateStatus:authView];
	// Also set userAuthorized for bindings
	[self setUserAuthorized:NO];
	[authView setAutoupdate:YES];
	[authView setDelegate:self];


The authView is an IBOutlet set up in IB pointing to the SFAuthorizationView.  Clicking on the lock when the program runs does all the right things, and I have several delegate methods implemented that fire as expected.  If the user quits when authView is still unlocked, part of what transpires is this call:

    
	[authView deauthorize:authView];


That works fine.  However, when I put this call in anywhere it does nothing (I've tried awakeFromNib: and an action triggered by an NSButton I added to the UI):

    
	[authView authorize:authView];


Any thoughts on what I may be forgetting to do?  Or am I misunderstanding the method's purpose?
Thanks!

----
S�bastien Gallerand: your question really had nothing to do with this thread.  I've moved it to LaunchdLaunchingAgain.

