---
layout: page
title: NSWindowFrameAutosizing
---

There are a number of bugs relating to NSWindow frame autosaving. If requested, Cocoa is supposed to save a window's position and size automatically, every time the window is moved or resized. When the window is shown again, it's supposed to restore these values. Unfortunately, as of Mac OS X 10.1.5, there are a number of bugs in this mechanism.

Here are my guesses as to the problems, and some workarounds. Corrections welcome.  --NicholasRiley

If everything works properly, you should be able to type a unique "Auto Save Name" in Interface Builder's Attributes Info for a NSWindow or NSPanel, and that's it.  Cocoa will add one default per window to the defaults database, which will look like this:

    
    "NSWindow Frame DockCam Status" = "13 742 421 150 0 0 1600 1178 "; 


In this case, "DockCam Status" is what I typed as "Auto Save Name".  Except that didn't work!

One problem that often occurs is that the frame autosave name you set in Interface Builder is not set when the window is unarchived.  Here's a workaround to be added in the window controller's code:

    
- (void)awakeFromNib;
{
    // XXX workaround for bug in 10.1.5 (and earlier?):
	// autosave name set in IB doesn't show up
    [self setWindowFrameAutosaveName: @"DockCam Status"];
    // self window] setFrameAutosaveName: @"[[DockCam Status"];
        
}


Either of the above lines will work, although as you'll see below they aren't entirely problem-free.

----

For windows that don't have NSResizableWindowMask set (e.g. custom-shaped windows), only the position will be restored automatically. You have to call     self window] setFrameUsingName:@"[[DockCam Status" force:YES]; in your controller's awakeFromNib to restore the size as well.

----

Another acknowledged bug (only happens with nonresizable windows?) is that the window's frame is not saved when the window is moved.  Here's a workaround, to be inserted in the window controller (or delegate?):

    
// XXX workaround for bug in 10.1.5 (and earlier?): no autosave on window move
- (void)windowDidMove:(NSNotification *)aNotification
{
    NSString *autosaveName = self window] frameAutosaveName];
    // XXX on initial display, we get a notification inside -[[[NSWindow setFrameAutosaveName]!
    if (autosaveName != nil)
        self window] saveFrameUsingName: autosaveName];
}


Note the check for autosaveName != nil: it turns out that either setFrameAutosaveName: or setWindowFrameAutosaveName: triggers the windowDidMove: notification which is automatically received by a window controller (or delegate?).  Here's what the stack looks like:

    
#0  -[[[DockCamStatus windowDidMove:]
[...]
#12 0x70c83360 in -[NSWindow _setFrameAutosaveName:changeFrame:] ()
#13 0x70c90e74 in -[NSWindow setFrameAutosaveName:] ()
#14 0x70c7df4c in -[NSWindowController setWindowFrameAutosaveName:] ()
#15 0x000039ac in -[DockCamStatus awakeFromNib]


DockCamStatus (my NSWindowController subclass) sends setWindowFrameAutosaveName:, which turns right around and sends setFrameAutosaveName:.  This only happens once, and while restoring the previously set window position _already present_ in the NSUserDefaults, so there's no harm in ignoring the notification.

----

Another problem you may encounter is that your window ends up offscreen, or with nonsensical coordinates.  The window in this example is resizable, and Cocoa has no problem saving its position and size without extra help, at least initially.  On subsequent launches/quits of your program, the frame is saved:

    
    "NSWindow Frame DockCam" = "10 906 360 266 0 0 1600 1178 "; 
    "NSWindow Frame DockCam" = "10 -2147483648 360 -2147483648 0 0 1600 1178 "; 


The problem actually occurs restoring the window's frame on launch after the first, correct frame is written.  This corruption only affects the vertical position and size of the window.

I haven't discovered what causes this particular problem.  In an effort to diagnose it, I removed the autosave name from Interface Builder and set it manually in awakeFromNib:

    
- (void)awakeFromNib;
{
    NSLog(@"frame is %@", NSStringFromRect([imageWindow frame]));
    NSLog(@"autosave frame is %@", [[NSUserDefaults standardUserDefaults] stringForKey: @"NSWindow Frame DockCam"]);
    [imageWindow setFrameAutosaveName: @"DockCam"];
    NSLog(@"after autosave name set, frame is %@", NSStringFromRect([imageWindow frame]));
// ... more code ...


First run:
    
2002-06-29 01:48:40.236 DockCam[4150] frame is { {10, 824}, {446, 348} }
2002-06-29 01:48:40.252 DockCam[4150] autosave frame is (null)
2002-06-29 01:48:40.253 DockCam[4150] after autosave name set, frame is { {10, 824}, {446, 348} }


Second run (after moving the window slightly):
    
2002-06-29 01:50:22.977 DockCam[4369] frame is { {10, 824}, {446, 348} }
2002-06-29 01:50:22.979 DockCam[4369] autosave frame is 10 906 360 266 0 0 1600 1178 
2002-06-29 01:50:22.984 DockCam[4369] after autosave name set, frame is { {10, 906}, {360, NaN} }


A perfectly valid default is incorrectly restored.  I have no idea why this is happening, but if I remove setFrameAutosaveName: and replace it with setFrameFromString:...

    
    [imageWindow setFrameFromString: @"10 906 360 266 0 0 1600 1178"];


it's still broken.  Saving is not broken, just loading, and the broken method appears to be -[NSWindow _setFrameFromString:force:]:

    
#0  0x70d9e844 in -[NSWindow _setFrameFromString:force:] ()
#1  0x70d9ebcc in -[NSWindow _setFrameUsingName:domain:force:] ()
#2  0x70da22d8 in -[NSWindow setFrameUsingName:] ()
#3  0x70c83360 in -[NSWindow _setFrameAutosaveName:changeFrame:] ()
#4  0x70c90e74 in -[NSWindow setFrameAutosaveName:] ()
#5  0x00002c2c in -[DockCam awakeFromNib]


Here's an extremely naive parser for the autosave frame:

    
- (void)awakeFromNib;
{
    NSArray *autosaveFrame = [[[NSUserDefaults standardUserDefaults] stringForKey:
@"NSWindow Frame DockCam"] componentsSeparatedByString: @" "];
    NSRect frame;
    [imageWindow setFrameAutosaveName: @"DockCam"];
    if (autosaveFrame != nil && [autosaveFrame count] == 9) {
        frame.origin.x = autosaveFrame objectAtIndex: 0] floatValue];
        frame.origin.y = [[autosaveFrame objectAtIndex: 1] floatValue];
        frame.size.width = [[autosaveFrame objectAtIndex: 2] floatValue];
        frame.size.height = [[autosaveFrame objectAtIndex: 3] floatValue];
        [imageWindow setFrame: frame display: NO];
    }


It works well enough for my purposes, obviously not handling edge cases as well as Apple's.  OTOH, my parser doesn't have the 'feature' of changing numbers to [[NaN randomly, so perhaps it isn't so bad after all. :-)

So far, so good, but the window opens apparently empty: the NSViews inside the window had their bounds corrupted by trying to fit inside a window with bogus bounds in [imageWindow setFrameAutosaveName:].  To avoid this side effect, we can't set the autosave name, so we need to explicitly save the window's frame whenever it changes.  The final version of the workaround for this bug is as follows:

    

- (void)awakeFromNib;
{
    NSArray *autosaveFrame = [[[NSUserDefaults standardUserDefaults] stringForKey:
@"NSWindow Frame DockCam"] componentsSeparatedByString: @" "];
    NSRect frame;
    // XXX workaround for bug in 10.1.5 (and earlier?): loading from autosave corrupts frame
    if (autosaveFrame != nil && [autosaveFrame count] == 9) {
        frame.origin.x = autosaveFrame objectAtIndex: 0] floatValue];
        frame.origin.y = [[autosaveFrame objectAtIndex: 1] floatValue];
        frame.size.width = [[autosaveFrame objectAtIndex: 2] floatValue];
        frame.size.height = [[autosaveFrame objectAtIndex: 3] floatValue];
        [imageWindow setFrame: frame display: NO];
    }
    // ...
}

// XXX workaround for bug in 10.1.5 (and earlier?): loading from autosave corrupts frame
- (void)windowDidMove:([[NSNotification *)aNotification
{
    [imageWindow saveFrameUsingName: @"DockCam"];
}

- (void)windowDidResize:(NSNotification *)notification;
{
    [self windowDidMove: notification];
}


Replace "DockCam" with your preferred autosave name in the code above.

----
To implement per-document window frame autosizing in an NSDocument based app (as recommended by jcr from apple), just put this in awakeFromNib of your window controller:

    
// window frame autosave
[self setShouldCascadeWindows: NO];
[self setWindowFrameAutosaveName: self document] fileName;


----

I just tried the above code without success. When I used NSLog to check the fileName, it came back as (null). Do I need to set up preferences somewhere, or is this code that's no longer valid under 10.4, or something else entirely?

Thanks!

----

I just ran into this in AdiumX under Tiger, with the authorization request panel (displayed when a user wants to add you to their contact list).

turns out that for an NSWindow (including NSPanel) owned by an NSWindowController, you must call     -[NSWindowController windowFrameAutosaveName], not     -[NSWindow frameAutosaveName].

reference: http://trac.adiumx.com/changeset/12707

--*boredzo*

----

A problem occurs when you can have more than one instances of the same type of window, e.g. in a document based application.
Above is one solution. But it has several problems, such as for new documents there's no fileName yet, and also the fileName may change after a Save As.
Or it may not be a document window at all. Here's another solution that makes no assumption on there being some kind of unique identifier.
It first tries to set the autosave name, which will work for the initial window. When that fails, which will happen for the subsequent windows, 
it will cascade the window from the previous window. So it is a combination of autosaving and cascading, getting the best out of both.
The method can be defined in an NSWindowController category and can be reused by various window controller classes.

    
- (void)setWindowFrameAutosaveNameOrCascade:(NSString *)name {
    static NSMutableDictionary *nextWindowLocations = nil;
    if (nextWindowLocations == nil)
        nextWindowLocations = [[NSMutableDictionary alloc] init];
    
    NSValue *value = [nextWindowLocations objectForKey:name];
    NSPoint point = [value pointValue];
    
    self window] setFrameUsingName:name];
    [self setShouldCascadeWindows:NO];
    if ([[self window] setFrameAutosaveName:name] || value == nil) {
        [[NSRect windowFrame = self window] frame];
        point = [[NSMakePoint(NSMinX(windowFrame), NSMaxY(windowFrame));
    }
    point = self window] cascadeTopLeftFromPoint:point];
    [nextWindowLocations setObject:[[[NSValue valueWithPoint:point] forKey:name];
}

