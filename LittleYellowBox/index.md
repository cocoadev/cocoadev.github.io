---
layout: page
title: LittleYellowBox
---

Does anyone know the API to set the string in the "little yellow box" that pops up when the mouse is over a button? I would like to use this "little yellow box" when a row is selected in a table view. --zootbobbalu

*The word you're looking for is "ToolTip". With that, you should be able to find lots of material.*

cool thanks, --zootbobbalu

----

OK, there is a bunch of disscussion about ToolTips, but I tried to implement this and have not been able to set the tool tip durring a     mouseDown: event. I'm going to search the Web, but if someone knows why simply setting a ToolTip string using     setToolTip: doesn't work please post here. --zootbobbalu

----

OK,  here's what I figured out. After putting this aside for half a day I decided to google "ToolTip NSView" and came up with this:

http://cocoa.mamasam.com/COCOADEV/2003/05/1/63277.php
(NOTE: as of 6/2007 this link no longer works)

    

    NSHelpManager *helpManager = [NSHelpManager sharedHelpManager];
    NSAttributedString *help = 
        [[[NSAttributedString alloc] initWithString:@"Text For Tip"] autorelease];
    [helpManager setContextHelp:help forObject:self];
    [helpManager showContextHelpForObject:self locationHint:[NSEvent mouseLocation]];


You have to remove this context help after you are done with it:

    
    NSHelpManager *helpManager = [NSHelpManager sharedHelpManager];
    [helpManager removeContextHelpForObject:self];


    self in this example is for an NSView subclass. --zootbobbalu

----
Neat! The ToolTip class I wrote (the bottom one) gives you a bit more control but if I had known about NSHelpManager I probably wouldn't have bothered.
----
The above code to remove the help window does not cause it to go away immediately - the user needs to press the mouse once to dismiss it. I wanted to be able to pop a window when the control key is pressed, and dismiss it when the key is raised. I tried a number of techniques to make the dialog go away,  many of which crashed my app ([helpManager release]!).

What I finally did was post a set of (fake) mouse down/up events, and the box disappears. Not really the best solution, but it works.

    
    NSHelpManager *helpManager = [NSHelpManager sharedHelpManager];
    [helpManager removeContextHelpForObject:self];
    NSEvent	*newEvent;
		
    newEvent = [NSEvent mouseEventWithType:NSLeftMouseDown
        location:self window] mouseLocationOutsideOfEventStream]
        modifierFlags:0
        timestamp:0
        windowNumber:[[self window] windowNumber]
        context:[[self window] graphicsContext]
        eventNumber:0
        clickCount:1
        pressure:0
    ];
    [[[NSApp postEvent:newEvent atStart:NO];
    newEvent = [NSEvent mouseEventWithType:NSLeftMouseUp
        location:self window] mouseLocationOutsideOfEventStream]
        modifierFlags:0 
        timestamp:0
        windowNumber:[[self window] windowNumber]
        context:[[self window] graphicsContext]
        eventNumber:0
        clickCount:1
        pressure:0
    ];
    [[[NSApp postEvent:newEvent atStart:NO];

