---
layout: page
title: SheetDroppingBehindWindow
---

I have a single-window application.  I am bringing up a sheet using
    
[NSApp beginSheet:sheet modalForWindow:[self window] modalDelegate:self didEndSelector:nil contextInfo:nil];


and then dismissing it using

    
	[NSApp endSheet:sheet];
	[sheet orderOut:nil];


Sometimes when I run my app everything works as expected. Other times (no code change, literally just a recompile) clicking anywhere outside the sheet will make the sheet drop behind the window and disappear except a thick black line where it once was glued to the titlebar.  The sheet can still be dismissed with keyboard events.

Changing the sheet characteristics in IB (like oneShot, buffered, etc) has no effect.
Using [NSApp runModalForWindow:sheet] followed by [NSApp abortModal] also has no effect.
I have several sheets which I order in using the exact same code, but from widely differing locations in the program's flow.  Yet on any given run of the app, all the sheets either work or all of them are broken.

It's the unpredictability of this that has me worried.  Has anyone seen anything like this before?

