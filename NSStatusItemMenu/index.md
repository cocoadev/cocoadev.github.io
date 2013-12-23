---
layout: page
title: NSStatusItemMenu
---



So I've created a MailBundle which displays an NSStatusItem showing the number of unread email messages in Mail.app.  I want the NSStatusItem to have a menu, but it didn't work for the most current release.  I rebuilt my program from scratch, and I've found out what the problem is.  I have a system in place which sends out an NSNotification when the unread count should be updated, and it calls this function:

    
- (void) updateMailUnreadMenuCount:(NSNotification *)notification
{
	[statusItem setTitle:@"c"];
	
	// Run Applescript To Get Unread Count
	NSString *unreadCount = [[NSString alloc] init];
	bool returnValue = [self executeAppleScript:@"getUnreadCount" withNSString:unreadCount];
	
	// Set Image For NSStatusItem
	[statusItem setImage:menuIcon];
	
	// Show Unread Value Based On Preferences
	if( returnValue )
		[statusItem setTitle:unreadCount];
	else
		[statusItem setTitle:@"e"];
}


Now, if I remove the third line of code (the one that sets the title to "c") then the NSMenu that is attached to the NSStatusItem will not display when the status item is clicked.  However, if I include the line of code, the menu shows just fine.  Does anyone know why this is?  It seems extremely odd, and I can't find a logical reason for it.  Thanks for any suggestions.  -- LoganRockmore

----

alright, so I looked into it a little further, and here's what I found.  I got rid of the third line altogether (which is exactly how I want the code to be).  Initially, when I start the program, clicking on the NSStatusItem does absolutely nothing.  I have another NSStatusItem program in the menu bar as well.  If I quit this second program, and then relaunch it, this basically moves the Mail NSStatusItem a little to the right.  After doing this "refreshing", the menu works just fine, as it should.

So, I guess there needs to be some way to programatically refresh the NSStatusItem, in order to get the menu to work.  Does this make any sense to anyone?  Thanks.  -- LoganRockmore

