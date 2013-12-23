---
layout: page
title: CreatingInspectorPanelForDocumentBasedApplication
---

With the term "inspector panel/palette" i mean singleton NSPanel window with it's NSWindowController, Nib, what is used represent some aspects of active NSSDocument subclass or just the window as seen in Photoshop (for example layers panel), Pages (Inspector) and so on. To create that shiny, shaddow casting panel, use the same technique as for other singleton windows+controller+nib packs (think "Preferences..." window).

Here I'll try to describe how i set up synchronizing the document window with inspector panel.

So, I assume you have 
* NSDocument subclass (i'll call it MyDocument) with one or more NSWindowController(s), 
* the InspectorController (NSWindowController) with it's Nib and NSPanel (or NSWindow) inside.
* in AppDelegate method for (lazy-)initializing and showing InspectorController's window

1) add something along those lines in MyDocument's NSWindow's delegate:
    
- (void)windowDidBecomeMain:(NSNotification *)notification
{
	[[NSNotificationCenter defaultCenter] postNotificationName:MyDocumentDidChangeNotification object:self];
}

- (void)windowWillClose:(NSNotification *)notification
{
	//not interested in not-active document close notifications as they are not related to inspector panel
	if([[NSDocumentController sharedDocumentController] currentDocument] == self) {
		[[NSNotificationCenter defaultCenter] postNotificationName:MyDocumentWillChangeNotification object:self];
	}
}

This will send notifications whenever window becomes main and closes. Those notifications the Inspector will observe.

2) somewhere in inspector's NSWindowController's init method add observers to notifications:
    
[[NSNotificationCenter defaultCenter] addObserver:self 
   selector:@selector(documentDidChange:) name:MyDocumentDidChangeNotification object:nil];

[[NSNotificationCenter defaultCenter] addObserver:self 
   selector:@selector(documentWillChange:) name:MyDocumentWillChangeNotification object:nil];


and notification callbacks:
    
- (void)documentDidChange:(NSNotification *)notification
{
	MyDocument *document = [notification object];
	[self setTargetDocument:document];
}

- (void)documentWillChange:(NSNotification *)notification
{
	[self setTargetDocument:nil];
}


Some more info MakingNibsTalkToEachOther, HasPantherChangedHowToDoNibs

-- ArnisVuskans

----

