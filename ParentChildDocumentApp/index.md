---
layout: page
title: ParentChildDocumentApp
---

I'm trying to figure out how to get documents in a Doc-based app to have a parent child relationship.  In my project, I have a ProjectDoc and a TextDoc.  I want the ProjectDoc to be the parent of the TextDoc.

The problem is when I create a new TextDoc, I don't have a way for it to be able to find it's parent ProjectDoc.  When a new TextDoc is created, I want it to find current active ProjectDoc and become it's child.

The program is set up with the two Documents (ProjectDoc and TextDoc), two window controllers (MainWindowController, TextWindowController), and three NIB files (MainMenu, MainWindow, TextWindow).  The two controllers own the last two nibs.ProjectDoc keeps track of and creates all the NSWindowController objects.  It only adds the MainWindowController to itself though.  So MainWindowController is loaded with MainMenu.nib and TextWindowController is loaded with TextWindow.nib.  What I want is for any new TextDoc to look up the TextViewController for the current ProjectDoc and add the controller to the TextDoc.  The FAQ on NSDocument says that passing WindowControllers around is the way to get a file browser/inspector app which is part of what I'm doing.

Something I tried doing was having an outlet of NSWindow type in the TextWindowController which would point to the window in MainWindow.  From there I added some code in the makeWindowControllers method of TextDoc to create a temp windowController, use the outlet in that windowController to get the mainWindow in the MainWindow.nib, call on NSDocumentController's documentFromWindow to get the parent ProjectDoc, add the ParentDoc's TextWindowController to the TextDoc, and finally free up the temp variable.  That didn't work because the outlet from the controller to the window didn't work.  IB would allow me to make the connection, but then if I tried to open the nib in the same session, IB would just crash out.  If I opened the nib on a different session, it would revert the nib back to not having the outlet connected.  Is there something odd about having outlets from one nib file to another?

I was also thinking about calling NSApplication's mainWindow but I don't know what window that's going to return.  The doc says it returns the mainWindow to the application but I don't know what that is.

mark

----
I am not sure there is just one way to do it, so I won't go into a detailed idea of what I would do, but here are a couple of possibilities:

*use NSNotifications to let the ProjectDoc know about the new TextDoc
*use a NSApplication delegate
*look into NSDocumentController (subclass it?? is is worth it?)

Then figure out the rest, you will learn a lot...

Also, I really believe you cannot connect two objects in 2 separate nibs. I did not even know that IB would let you do that. --CharlesParnot

----
Perhaps your problem might be solved by rethinking your project's design, and what the various windows/documents are intended to achieve.

Are ProjectDocs and TextDocs genuinely different sorts of documents (ie: totally distinct, and independently-useful bundles of information)? Or are TextDocs merely subcomponents of a ProjectDoc, with no reason for existing outside their ProjectDoc?

Since you're talking about a parent-child relationship, it's probably not the first option, so I don't think you really need separate subclasses of NSDocument.

Instead, I suggest that only your ProjectDoc be a subclass of NSDocument, and that it have sole responsibility for managing:

*the usual functions of an NSDocument, like saving to disk, loading from disk and so on;
*the overall structure of the project (ie: how many TextDocs there are, what they're called, how they're arranged with respect to one another, etc); and
*internal access to the subcomponents of the project for other (specific) editing classes, namely TextDocs.

You could then refactor your TextDocs as NSWindowControllers. The advantage of this approach is that Cocoa's document-based architecture already allows for multiple NSWindowControllers to be attached to a single document dynamically (in the makeWindowControllers method, for example), and the window outlet in NSDocument itself allows you to keep track of the "main" window.

Cheers

Tom

