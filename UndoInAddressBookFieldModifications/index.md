---
layout: page
title: UndoInAddressBookFieldModifications
---



  This isn't really a programming question (well, maybe it is, using APE..), but one thing I've noticed about AB that *really* bugs me (I consider it a bug) is this behavor:

I create a new card, then start editing the fields.  I type something wrong into one of the fields and hit Cmd-Z.  Instead of simply undoing the *text entry*, it undoes the enire card creation.  Now, of course, I can redo the card creation, but that's a pain, and it's unexpected.  If I'm editing text, I expect undo to undo the text editing ONLY, like in any other text editing application.

  I assume this is because AB doesn't register each keypress/field entry with the Undo Manager, probably for speed reasons.  I can think of two solutions:

1) Speed up the Undo Manager in General/AppKit
2) "Take over" the undo functionality (like in old-style pre-Cocoa) ONLY for the field entry mode on a card, and disable the "global" Undo Manager" while in the text-entry mode.

  I guess what I'm asking is: is anyone else bothered by this, and could the General/CocoaDev community here organize to put, say 600 bug/feature reports about this on General/BugReporter so it'll get noticed?

Jim

----

General/NSTextField<nowiki/>s don't have undo by defaults. See [http://members.shaw.ca/akochoi-old/blog/2004/08-22/index.html#4]

----

I don't think General/AddressBook uses General/NSTextFields.  The pane is an instance of a subclass of General/NSTextView.

http://www.cocoabuilder.com/archive/message/cocoa/2005/1/20/126260

General/FScriptAnywhere verifies this.
