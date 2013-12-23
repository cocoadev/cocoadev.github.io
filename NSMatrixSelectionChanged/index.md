---
layout: page
title: NSMatrixSelectionChanged
---

I am getting behavior from a checkbox that has me baffled.

I have an NSMatrix of checkboxes.  When the user clicks on a check I want to turn the "check" off (by setting it's state: NO).
This works everywhere in my controller object *except* in one method.  Here is a code snippet from that method showing what I'm trying to do:

    
	if ((employeeIndex != myIndex) && !(masterEmployeeArray objectAtIndex: [[sender selectedCell] tag checkedIn]) && !(masterEmployeeArray objectAtIndex: [[sender selectedCell] tag loggedIn])) {
		NSLog(@"he/she's NOT here");
		// unhighlight all names
		for (i = 0; i < [masterEmployeeArray count]; i++) {
			if (!masterEmployeeArray objectAtIndex: i] checkedIn]) {
				[[employeeMatrix cellWithTag: i] setBackgroundColor: [[PSLoggedOutColor];
				[infoWindow setStringValue: @""];
			}
		}
		employeeMatrix cellWithTag: employeeIndex] setState: [[NSOffState];  //<----here's the issue.  the checkbox remains checked.
		employeeMatrix cellWithTag: employeeIndex] setBackgroundColor: [[PSSelectedPersonColor];
		[infoWindow setStringValue: masterEmployeeArray objectAtIndex: [[sender selectedCell] tag message]];
	}


And the checkbox remains "checked" or in NSOnState state.  When I add     NSLog(@"state = %i", employeeMatrix cellWithTag: employeeIndex] state]); I get exactly what I'm expecting, but the box remains checked (or unchecked) depending.
What gets me confused is that I'm doing  similar thing elsewhere and it works.  In fact,     [[employeeMatrix cellWithTag: employeeIndex] setBackgroundColor: [[PSSelectedPersonColor]; works.

Any insight would be appreciated.

'edit: 'YES changed to NSOnState, NO to NSOffState etc.*
----
Try doing a     [matrix setNeedsDisplay:YES] after changing the cell state, as it won't automatically know that it has to redraw itself when you fiddle with a cell directly.

----

I believe this is a classic problem. See here: http://www.cocoabuilder.com/archive/message/cocoa/2002/3/19/66603

----

In brief, I was trying to change the state of a matrix cell from within my matrix's IBAction method (-(IBAction)matrixSelectionChanged:(id)sender). The selectCellWithTag: method call was completely ignored. I used a delayed perform and it worked beautifully. 
----
How might something like that look?  Would the selector be the setState: method and the object the NSButtonCell?  How long does the delay need to be?
----
The delay can be 0 seconds, which means basically 'execute this after the current run loop cycle'.  Clicking a checkbox button changes the state of the button automatically - problem here is that it isn't changed until after the action is sent.  Thus any changes you make to the button's state in the action method get flattened by the subsequent change.

----

Unfortunately, this brute-force approach is necessary due to what amounts to a bug in AppKit (if you read the thread mentioned above). I use this approach successfully. If you need to do something with the state, store it for as long as the method 'lives', then set the appropriate state using the delayed perform method. Setting state from within a matrix's action method should 'just work', but it doesn't, and this work-around is not only acceptable (and works well) but is the only solid way around this problem.

----

I have a matrix made of two radio buttons. I tried to find a delegate method that would ressemble this :

selectionDidChange

Or something like that. I have a method called everytime the selection is changed. But I don't want the method to be called if the user presses the radio button that is already selected...

----

So check your internal state. If it's the same as the selected button, don't do anything

