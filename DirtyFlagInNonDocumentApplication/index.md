---
layout: page
title: DirtyFlagInNonDocumentApplication
---

Hi,

I'm trying to modify the dirty flag in a non-document application (the black point in the red one). How shall I do that ? By implementing a delegate method ? which one ? Is it possible to bind it to [undoManager canUndo] ?

I have alredy implemented the "- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)sender" and it work fine with the Undo/Redo of the edit menu.

Thanks in advance =)

----

NSWindow: - (void)setDocumentEdited:(BOOL)flag
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWindow.html#//apple_ref/occ/instm/NSWindow/setDocumentEdited:

----

I do have a document-based application and I still can't figure out how to get the document edited status to show in the window without a whole lot of pain (manual updating from the document).  Is there no way to have it set automagically from the undo manager?  If not, it seems like a glaring omission considering how much pain Apple seems to have gone to eliminate "glue code" with bindings and whatnot.

Edit: Nevermind.  I figured out that I was subclassing NSWindowController, but not attaching the "window" outlet for it to the window in my document's NIB file.

