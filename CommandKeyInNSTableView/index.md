---
layout: page
title: CommandKeyInNSTableView
---



I've subclassed NSTableView to get keyDown events.

I've implemented this
    
- (void) keyDown:(NSEvent *)event
{
	unichar key = event charactersIgnoringModifiers] characterAtIndex:0];
	if (key == [[NSDeleteCharacter && !cmd && [self numberOfRows] > 0 && [self selectedRow] != -1)
		self delegate] removeRows:self warning:YES];
	else if(key == [[NSDeleteCharacter && cmd && [self numberOfRows] > 0 && [self selectedRow] != -1)
		self delegate] removeRows:self warning:NO];
	else
		[super keyDown:event];
}

- (void) flagsChanged:([[NSEvent *) theEvent
{
	int flags = [theEvent modifierFlags];
	cmd = (flags & NSCommandKeyMask) ? YES : NO;
}


The problem is that when i have cmd selected and press the delete key, I get a beep

Does this sounds familiar?

----

Well, I believe that command key events take a different path.  See http://developer.apple.com/documentation/Cocoa/Conceptual/BasicEventHandling/Concepts/KeyEventPath.html and click through to the "Key Equivalents" page.  That page says that only menu items should have key equivalents, but I think it's actually menu items and buttons.  

I take it cmd is a global that indicates whether the command key is pressed?  You don't have to do that.  You can check the event that's passed in.   In situations where you don't have ready access to the current event, you can get it with     -[NSApplication currentEvent].

----

Command equivalents don't get sent to     -keyDown:, they get sent to     -performKeyEquivalent:. Override that one as well, and you should be set. *..except that Apple says that arbitrary controls should not have key equivalents, as discussed in the link.  If you don't care, then yes, this would work.*

ObIfAppleDoesntCareWhyShouldWe: the code above deletes a row from a table view when cmd-delete is pressed. You know, just like every application from Apple (and most anybody else) does.

*
Which apps are those?  I'm not saying you're wrong, but I don't know any.  In Mail cmd-delete is the shortcut of a menu item, not the key equivalent of a table.  In Address Book cmd-delete doesn't do anything, delete does.  Bookmarks in Safari are also deleted with delete, not cmd-delete.

The code above does not look useful to me.  Make delete work without a warning, and support undo.
*

----

Tnx

