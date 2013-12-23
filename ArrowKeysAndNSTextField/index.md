---
layout: page
title: ArrowKeysAndNSTextField
---

I'm new to Cocoa.  I'm currently designing an app that uses a subclassed NSTextField and an NSTableView to present a list of choices to the user and filter those choices.  That all works fine.  My only problem is that I want the arrow keys to arrow up and down the list of possible items in the table view and do a few other nice goodies, but I can't seem to get notified of those key presses no matter what I do.  I found:

http://www.cocoabuilder.com/archive/message/cocoa/2004/10/27/120330

It says to overload the NSResponder methods, but I'm not seeing anything in those either.  I tried the following keyDown: to no avail as well:

    
- (void)keyDown:(NSEvent *)theEvent
{
	NSLog([theEvent characters]);
	unichar unicodeChar;
	NSString *characters;
	characters = [theEvent characters];
	unicodeChar = [characters characterAtIndex:0];
	if (unicodeChar == 0x0009)
	{
		NSLog(@"Up");
	}
}


It never seems to get called.

I already use controlTextDidChange to filter the list, and it doesn't give me anything for the arrow presses...  What's next? --Blarg

----
See FieldEditor.

----
I never updated this with my solution.

In the TextView's delegate, you can implement
- (BOOL)control:(NSControl *)control textView:(NSTextView *)fieldEditor doCommandBySelector:(SEL)commandSelector

This gets the important key events we couldn't pick up before and completely avoids dealing with key events.

Here's a skeleton implementation.

    
- (BOOL)control:(NSControl *)control textView:(NSTextView *)fieldEditor doCommandBySelector:(SEL)commandSelector
{
	BOOL returnValue = NO;
	
	//NSLog(@"TextField Action: %@", [NSString stringWithCString:commandSelector]);

	if (commandSelector == @selector(insertNewline:))
	{
		// Set returnValue to YES to have the text view ignore the command.
		returnValue = YES;
	}
	else if (commandSelector == @selector(moveDown:))
	{
	}
	else if (commandSelector == @selector(deleteBackward:) || commandSelector == @selector(deleteForward:))
	{
	}

	return returnValue;
}


----
Off the subject, but a safer way to turn a selector into an NSString is to use NSStringFromSelector.

