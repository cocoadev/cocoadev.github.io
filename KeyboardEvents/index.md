---
layout: page
title: KeyboardEvents
---



Everything and anything related to keyboard events.

[Topic]

The easiest way to capture keyboard events from IB elements (e.g. NSTableView) is to subclass the particular IB element and then edit the custom class setting in IB.


*create a subclass of NSTableView in PB named MyTableView
*add the method "-(void)keyDown:(NSEvent *)theEvent" to the new subclass
*open the MainMenu.nib in IB
*save all the files in PB and drag MyTableView.m onto the MainMenu.nib window
*drag an NSTableView from the Tools Pallet and drop into the main window
*click on the new NSTableView, hit command + 5 and then select the Custom Class "MyTableView"


The best way to implement the keyDown method in your custom class is to pass the key down event on to the super class:

    
-(void)keyDown:(NSEvent *)theEvent {
    NSLog(@"MyTableView: keyDown: %c", theEvent characters] characterAtIndex:0]);
    [super keyDown:theEvent];
}


[[NSTableView<nowiki/>s play two major roles, one role is to display large amounts of data in an organized fashion. The other role is to support text editing. The first role does not require a text editor, so while an NSTableView is in "view" mode the subclass MyTableView is able to receive all keyboard event (i.e. flagsChanged, keyDown and keyUp). 

When the user double clicks on an item in a table view, the table view starts to play the role of text editor (assuming the table view is configured to allow editing). When a cell is placed in edit mode, the responder chain becomes modified and the table view no longer receives keyDown and keyUp events. This is usually not a problem, but sometimes this behavior can be undesirable. The reason the table view no longer receives all keyboard event is because the cell within the table view has introduced the FieldEditor into the responder chain. 

The FieldEditor is simply an NSText object, but this object usually supports all of the text editing done for any text based object (NSTextField, NSTextView, NSCell...). Some keyboard behaviors can be modified for objects that depend on the FieldEditor, but getting complete control over text based IB elements is not an easy task. A major issues with keyboard events and the FieldEditor is the FieldEditor essentially captures most keyboard events and doesn't pass them on to the next responder. This behavior has a purpose, but sometimes more control is desired. 

The documented solution is to implement a custom FieldEditor, so when I get some time I'll explain how to do this. Any help here would be great. 

sorry :-) 

http://www.epinions.com/user-review-7959-581C4E7-39207196-prod2

---- 
...what does that link at the end have to do with anything?? Oh, wait... is it because the O.P. used "i.e."?
----

Anybody know how to receive keyboard events globally?

----

Yeah, I wouldn't mind knowing how to do that also!!

----

This article, by the developers of the awesome LaunchBar, is relevant:
http://www.obdev.at/developers/articles/00001.html

----

Wow, they describe a seriously heavyweight method in the article. Anybody know how they do it in Synergy ( http://synergy.wincent.com )?

----

HotKeys may be the answer

----

I'm trying to implement buttons that change titles to their keyboard short cuts when the user holds down the control key.  I was able to get this working just fine by subclassing NSWindow and implementing the flagsChanged function and passing it on to the class that is listening.  The problem I'm having is the associated keyDown events aren't getting passed along if a text field has the keyboard focus.  How do I get those keyboard event first, before the are caught by the control?

    

-(void)flagsChanged:(NSEvent *)event
{
	NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
	[nc postNotificationName:@"modifierKeyChanged" object:event];
}

-(void)keyDown:(NSEvent *)event
{
    if ([event modifierFlags] & NSControlKeyMask) {
		NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
		[nc postNotificationName:@"shortCutKey" object:event];
    }
}


OK.  Here's the solution.  Subclass NSApplication and over-ride the sendEvent function as shown below.  Update your nib and set the Custom Class of the File's Owner in your nib to your new subclass.  The only tricky thing about this is you need to update your info.plist file to reflect the name of your new NSApplication subclass.  Hope this helps.

    
- (void)sendEvent:(NSEvent *)event
{
	switch ([event type]) 
	{
		case NSKeyDown:
		    if ([event modifierFlags] & NSControlKeyMask) {
				NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
				[nc postNotificationName:@"shortCutKey" object:event];
		    }
			break;
		default:
			break;
	}
	[super sendEvent:event];
	
}

