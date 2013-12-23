---
layout: page
title: DoubleClickMultipleRowsInTableView
---

One of the windows in my app contains a table view with a list of files. I have a <code>doubleAction</code> set up so that I can double-click on a row to open that file. The problem is: if multiple rows are selected, a double-click changes the selection so that only the clicked-on row is selected. This means that I can't select several files and then double-click once to open them all; I have to double-click on each one separately.

I hasten to note that this behavior is "correct" according to the AquaHumanInterfaceGuidelines: a double-click is supposed to be an extension of a single-click, which deselects all rows except the one clicked on. The guidelines notwithstanding, many applications (Finder among them) allow double-clicking on several table rows at once.

Getting the desired behavior is a bit tricky, so I wanted to share my solution in case it is useful to anyone else. 

My first attempt used the delegate method <code>selectionShouldChangeInTableView:</code> to prevent deselection when one of multiple selected rows is clicked on. This doesn't work, however--it turns out that <code>selectionShouldChangeInTableView:</code> is called when rows will be added to the selection, but not when rows will be deselected.

The second attempt, which was ultimately successful, was to subclass NSTableView and override <code>mouseDown:</code> to discriminate between single and double clicks. The idea is to forward genuine single clicks to <code>super</code>, but not double clicks. Of course, at the beginning of a double-click the table view has no way of knowing whether the click will be single or double until it waits for the second click to arrive (or not). 

We can implement this waiting period by calling the NSObject method <code>performSelector:withObject:afterDelay:</code> in the subclass's <code>mouseDown:</code> method. If a double-click event arrives in the meantime, we can call the NSObject class method <code>cancelPreviousPerformRequestsWithTarget:</code> to prevent the single-click.

The *obvious* thing to do is something like

    
- (void)mouseDown:(NSEvent *)theEvent
{
	BOOL doubleClick = ([theEvent clickCount] == 2);
	float	doubleClickThreshold = 1.0;

	if ( doubleClick == NO )
	{
		// pass the event to super after a delay
		[super performSelector:@selector(mouseDown:) withObject:theEvent afterDelay: doubleClickThreshold];
	}
	else
	{
		// cancel the single-click and perform the doubleAction
		[NSObject cancelPreviousPerformRequestsWithTarget:super];

		self target] performSelector:[self doubleAction;
	}
}


This doesn't work, though. For reasons that are not clear to me, the delayed     mouseDown: message is sent to the subclass, and not to     super. This creates an infinite loop of     mouseDown: messages, each delayed by     doubleClickThreshold. Not good. Even implementing a     superMouseDown: method to specifically forward the message to     super doesn't seem to work.

I finally solved the problem by overriding NSTableView's     selectRowIndexes:byExtendingSelection: method to disable the selection change when a single row of a multi-row selection is clicked on. I also implement a new method,     selectOnlyRowIndexes:, that accomplishes the deselection behavior and is called after the time delay. I also override the NSView method     dragImage:at:offset:event:pasteboard:source:slideBack: so that a multiple-row selection is preserved after dragging. As a final nicety, I check the user defaults to get the value of the     doubleClickThreshold.

Here's the code:

    
static unsigned int TAModifierKeyMask = NSShiftKeyMask | NSAlternateKeyMask | NSCommandKeyMask | NSControlKeyMask;

@implementation TAMultirowDoubleClickTableView

- (int)mouseRowForEvent:(NSEvent *)theEvent
{
    NSPoint mouseLoc = [self convertPoint:[theEvent locationInWindow] fromView:nil];
    
    return [self rowAtPoint:mouseLoc];
}

- (void)selectOnlyRowIndexes:(NSIndexSet *)rowIndexes
{
    [super selectRowIndexes:rowIndexes byExtendingSelection:NO];
}

- (void)selectRowIndexes:(NSIndexSet *)rowIndexes byExtendingSelection:(BOOL)flag
{
    NSEvent *theEvent     = [NSApp currentEvent];
    int      mouseRow     = [self mouseRowForEvent:theEvent];
    BOOL     modifierDown = ([theEvent modifierFlags] & TAModifierKeyMask) != 0;
    
    if ( self selectedRowIndexes] containsIndex:mouseRow] && (modifierDown == NO))
    {
        // this case is handled by selectOnlyRowIndexes
    }
    else
    {
        [super selectRowIndexes:rowIndexes byExtendingSelection:flag];
    }
}

- (void)mouseDown:([[NSEvent *)theEvent
{
    static float doubleClickThreshold = 0.0;
    
    if ( 0.0 == doubleClickThreshold )
    {
        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        doubleClickThreshold = [defaults floatForKey:@"com.apple.mouse.doubleClickThreshold"];
        
        // if we couldn't find the value in the user defaults, take a conservative estimate
        if ( 0.0 == doubleClickThreshold ) doubleClickThreshold = 1.0;
    }

    BOOL    modifierDown    = ([theEvent modifierFlags] & TAModifierKeyMask) != 0;
    BOOL    doubleClick     = ([theEvent clickCount] == 2);
    
    int mouseRow = [self mouseRowForEvent:theEvent];
    
    if ( self selectedRowIndexes] containsIndex:mouseRow] && (modifierDown == NO) && (doubleClick == NO) )
    {
        // wait to see if there is a double-click: if not, select the row as usual
        [self performSelector:@selector(selectOnlyRowIndexes:) withObject:[[[NSIndexSet indexSetWithIndex:mouseRow] afterDelay:doubleClickThreshold];
        
        // we still need to pass the event to super, to handle things like dragging, but 
        // we have disabled row deselection by overriding selectRowIndexes:byExtendingSelection:
        [super mouseDown:theEvent]; 
    }
    else if ( doubleClick == YES )
    {
        // cancel the row-selection action
        [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(selectOnlyRowIndexes:) object:[NSIndexSet indexSetWithIndex:mouseRow]];

        // perform double action
        self target] performSelector:[self doubleAction;
    }
    else
    {
        [super mouseDown:theEvent];
    }
}

- (void)dragImage:(NSImage *)anImage at:(NSPoint)imageLoc offset:(NSSize)mouseOffset event:(NSEvent *)theEvent pasteboard:(NSPasteboard *)pboard source:(id)sourceObject slideBack:(BOOL)slideBack
{
    // we are starting to drag a row(s), so cancel any pending call to change the row selection
    int     mouseRow = [self mouseRowForEvent:theEvent];
    [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(selectOnlyRowIndexes:) object:[NSIndexSet indexSetWithIndex:mouseRow]];
    
    [super dragImage:anImage at:imageLoc offset:mouseOffset event:theEvent pasteboard:pboard source:sourceObject slideBack:slideBack];
}

@end


Naturally, any comments/corrections are much appreciated.
ThomasAnderson

----
The reason that your first code failed is because     super is not an object, per se. When you put in a call to     super, the program just uses the superclass's implementation of the method. And the superclass's implementation of     performSelector:withObject:afterDelay: says to use     self's implementation of the selector. To get around this, try something like this (untested):
    
*...mouseDown: method...*
[self performSelector:@selector(superMouseDown:) withObject:theEvent afterDelay:doubleClickThreshold];
*...mouseDown: method...*

- (void)superMouseDown:(NSEvent *)theEvent
{
	[super mouseDown:theEvent];
}

Even though you said that you already tried something like this, I'd like to know how this version turns out (I'm on a PC right now, so I can't test this myself). It's also possible that the problem lies with the delay; by the time the event is processed, the run-loop may have already moved on. Maybe you should stall the main thread instead (or something; I'm just thinking out loud). Hope this helps (and works). --JediKnil

----
Thanks for the insight. I had tried exactly what you suggested, but it doesn't seem to work. The <code>superMouseDown:</code> method is called when it's supposed to, but it doesn't seem to do anything. I'd be very interested in knowing what's going on. In any case, the final code I posted is working perfectly for me so far....   --ThomasAnderson

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Hi Thomas 

If you send a mouse event on to the super without increasing the clickCount to 2 then super gets 2 single clicks.

Try this - my TableView works on single click rather thanm double click - which is what I want.


-(void)mouseDown:(NSEvent*)theEvent

{

   NSEvent *newEvent = [NSEvent mouseEventWithType:[theEvent type]

     location:[theEvent locationInWindow]

     modifierFlags:[theEvent modifierFlags]

     timestamp:[theEvent timestamp]

     windowNumber:[theEvent windowNumber]

     context:[theEvent context]

     eventNumber:[theEvent eventNumber]

     clickCount:2

     pressure:[theEvent pressure]];


   [super mouseDown:newEvent];

return;

}

Note that it is exactly the same event except that the clickCount is now 2 instead of a single click
Regards, Keith Wilson from Down Under

----

The example code ThomasAnderson posted has one small bug -- it doesn't fill in the sender parameter of the doubleAction selector, so it will segfault if you try to access that from within the doubleAction method.  The fix is to change the line:

    
		self target] performSelector:[self doubleAction;


to read:

    
		[[self target] performSelector:[self doubleAction] withObject:self];

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

