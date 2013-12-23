---
layout: page
title: NSMatrixOfRadioButtonsIssue
---



I have an NSMatrix of 2 radio buttons and I don't understand the behavior I am experiencing.
When the user clicks on one of the radio buttons, the matrix triggers the following method:
    
- (IBAction)lensAgeClicked: (id)sender
{
	NSLog(@"lensAgeClicked");
	int old = [sender cells] objectAtIndex: 0] state];
	int new = [[[sender cells] objectAtIndex: 1] state];
	[[NSLog(@"state = %i, %i", old, new);
	
}


The behavior I'm seeing is that I have to select a radio button twice in order to see the NSLog display      1, 0 or     0, 1.  Otherwise I get     1, 1

I'm sure there's something I'm not understanding.  I'm also sure that there is a better way to see the state of a radio button that has just been clicked.  Hopefully someone here can help me on either points.

Thanks!
----
I would guess that when your action method is called, the state is still switching somehow. You could try just using the much simpler     [sender selectedCell] or     [sender selectedTag], both NSControl methods. --JediKnil

----
Yes, not every cell has had its state updated when the action message is sent.      -[NSMatrix selectedCell] is what you want, like knil said.

