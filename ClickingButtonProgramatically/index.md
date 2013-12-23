---
layout: page
title: ClickingButtonProgramatically
---



How to perform button clicking action programatically? I have tried NSApp's sendAction:to:from still it is not working
    
	[NSApp sendAction: cancel selectedCell] action] to:[[cancel selectedCell] target] from:[self window;


----

It depends on what you're trying to do. If you're just trying to send the action message of a control, the code you posted ought to work (though self instead of [self window] is probably the appropriate sender.) If you're trying to get your NSView subclass to think it's been clicked with the mouse, you might want to send artificial NSEvent objects. If you're trying to simulate a click at a screen position, ignoring what you're clicking on, then you probably need to drop into Carbon. In that case, HIViewSimulateClick() may be the function you're looking for.

----
Try **performClick:** method from NSControl class to simulate one click on your control.

----
I just wanted to send action message. i have tried self, instead of [self window] still it is not working. [[cancel selectedCell] performClick:self] works. But i am wondering why code posted above is not working! 

----
Debug your code. Have you tried, say, printing out what     [cancel selectedCell],     [[cancel selectedCell] action], etc. are?

