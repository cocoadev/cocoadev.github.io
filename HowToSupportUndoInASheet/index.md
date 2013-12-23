---
layout: page
title: HowToSupportUndoInASheet
---

There is scant (or hard to find) information on how to support undo within a modal sheet in a Cocoa application. I've managed to get it working, so thought I'd document it here.

I came across the NSPersistentDocument Core Data Tutorial (http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/08_CreationSheet/chapter_9_section_6.html), which has a section on supporting Undo in a sheet.

Unfortunately, the code in the document doesn't do everything related to undo/redo - the menu titles aren't updated for some reason. To get undo/redo working properly in a sheet, follow these steps:

1. Implement the changes and workarounds mentioned in the tutorial (above).

2. Modify the targetForAction:to:from: method in your NSApplication subclass to modify the menu item titles, so it looks like:

    
- (id)targetForAction:(SEL)anAction to:(id)aTarget from:(id)sender
{
    if ((anAction != @selector(undo:)) &&
        (anAction != @selector(redo:))) {
		
        return [super targetForAction:anAction to:aTarget from:sender];
    }
	
    id keyWindowDelegate = self keyWindow] delegate];
    if ([keyWindowDelegate isKindOfClass:[[[YourSheetController class]]) {
		// Work-around the fact that the undo/redo action names don't show up in the edit recipe -- CHANGES START
		if (anAction == @selector(undo:))
			[sender setTitleWithMnemonic:[keyWindowDelegate managedObjectContext] undoManager] undoMenuItemTitle;
		else if (anAction == @selector(redo:))
			[sender setTitleWithMnemonic:[keyWindowDelegate managedObjectContext] undoManager] redoMenuItemTitle;
              // -- CHANGES END

        return keyWindowDelegate;
    }
    return [super targetForAction:anAction to:aTarget from:sender];
}


-- JeremyHiggs

