---
layout: page
title: NSTextViewScrollingOneLineTooFew
---

Hello. I'm trying to make a simple RPG engine, using some Open Sword code as a base (and may I say, you guys are awesome). Right now I'm trying to make a game log that keeps track of the player's actions a la Sword Dream 3D. It works, but when I log a new action and tell the NSTextView to scroll to the end, it scrolls to the end and *then* adds the new string (or that's what it seems to be doing). As a result, the log constantly appears to be one step behind the player.

My current code, where gameLog is the outlet to the NSTextView:
    - (void)translateAction:(NSNotification *)note
{
	if (note object] isEqual:[[OFUp]) {
		[self logAction:@"\nThe party moves North."];
	} else if (note object] isEqual:[[OFRight]) {
		[self logAction:@"\nThe party moves East."];
	} else if (note object] isEqual:[[OFDown]) {
		[self logAction:@"\nThe party moves South."];
	} else if (note object] isEqual:[[OFLeft]) {
		[self logAction:@"\nThe party moves West."];
	}
	NSPoint logEndPoint;
	logEndPoint.x = [gameLog bounds].origin.x + [gameLog bounds].size.width;
	logEndPoint.y = [gameLog bounds].origin.y + [gameLog bounds].size.height;
	[gameLog scrollPoint:logEndPoint];
}

- (void)logAction:(NSString *)gameAction
{
	NSAttributedString *action = [[NSAttributedString alloc] initWithString:gameAction];
	NSTextStorage *logText = [gameLog textStorage];
	[logText appendAttributedString:action];
	[action release];
}


What I'd like to know is if there's a way to fix this so that the log scrolls to the true end, short of subclassing and overriding everything in NSTextView.

----
One option is ConsoleView FastWritesToNSTextView

Another is - (void) appendMessage:(NSAttributedString *) message at http://www.cocoabuilder.com/archive/message/cocoa/2003/4/26/4493

or

http://www.cocoabuilder.com/archive/message/cocoa/2001/2/22/28382

----
Ah. Thanks very much - in addition to having the log work now, I feel like a fool for not knowing of the existence of CocoaBuilder beforehand. :)

