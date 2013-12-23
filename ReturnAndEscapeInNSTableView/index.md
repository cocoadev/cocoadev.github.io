---
layout: page
title: ReturnAndEscapeInNSTableView
---


----

It is possible to customize the behavior of table views so that return/enter doesn't start editing the next row etc. Unfortunately it is not very obvious how to do this. 


* Override keyDown: from NSResponder and take care of the keys you want to handle (forward the rest to super).
* Implement cancelOperation: which is called when you hit escape so that the editing is escaped (this should be the default behavior)
* Implement textDidEndEditing: from NSTextView, see if the editing ended because of return and take action accordingly


    
#pragma mark NSResponder

- (void)keyDown:(NSEvent *)theEvent
{
	if([theEvent keyCode] == 0x31)//Space
		[NSApp sendAction:@selector(doSomethingWithSpace:) to:[self delegate] from:self];
	else if([theEvent keyCode] == 0x24 && [self numberOfSelectedRows] == 1)//return
		[self editColumn:0 row:[self selectedRow] withEvent:theEvent select:YES];
	else
		[super keyDown:theEvent];
}

- (void)cancelOperation:(id)sender
{
	[self abortEditing];
	self window] makeFirstResponder:self];
}


#pragma mark [[NSTableView

- (void)textDidEndEditing:(NSNotification *)aNotification
{
	// what made editing stop?
	int movement = [aNotification userInfo] objectForKey:@"[[NSTextMovement"] intValue];
	int selectedRow = [self selectedRow];
	
	// pass on info to superclass, which will advance the field editor
	[super textDidEndEditing:aNotification];
	
	// was the movement a return?
	if(movement == NSReturnTextMovement)
	{
		// abort editing (you have to do it after calling [super textDidEndEditing:] or the changes will be ignored)
		[self abortEditing];
		[self selectRow:selectedRow byExtendingSelection:NO];
		
		// aborting has the strange side-effect of making the table view loose focus, so we re-activate it
		[[self window] makeFirstResponder:self];
	}
}


You should be able to use this code as a basis for further customizations of other keys. Good luck!

Information gathered from various mailing lists:


* http://www.cocoabuilder.com/archive/message/cocoa/2004/3/2/100569
* http://lists.apple.com/archives/Cocoa-dev/2007/Jan/msg00201.html


/Gabriel

