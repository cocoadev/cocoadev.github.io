---
layout: page
title: CurrentDocument
---

You can get the current document in a multi-document app from your shared document controller instance by sending it the message     currentDocument. One frequently does this because model data need to be associated with the "right" document. Here are some situations that have arisen in the past which are related to these matters.

**[[NSDocumentController sharedDocumentController] currentDocument]**

----

**How do I assign which NSDocument 'owns' my object? Well, if you have a model instance in the document, you can set an ivar!**

I've been writing software with Cocoa for over a year now and this is actually the first time I've been faced with this problem. What I'm trying to do is allow my data model object ("AssetModel")  to know which currently-open NSDocument is its "daddy". My NSDocument subclass (we'll stick with MyDocument) has an AssetArray. Each AssetModel is created on the fly, but needs to be able to ask the appropriate MyDocument instance for its fileWrapper path and many other things only an instance of my MyDocument subclass can answer.

I know I can get the currently *active* MyDocument by calling [[NSDocumentController sharedDocumentController] currentDocument] ... but if the AssetModel is doing something while its MyDocument instance is backgrounded (the user has brought another document forward), this will point to the wrong MyDocument, won't it? How do I get around this? What's the best way?

----

Do you create an AssetModel instance for each document? In that case, just set an ivar     [myAssetModel setDocument:(MyDocument*)thisDocument]

----

That'd do it. 

Well, actually, I create one AssetLibrary instance, then many assets are dynamically created and destroyed. BUT, it's the same thing, really. I guess as each AssetModel is created, I can link it to its document. It's only storing a pointer when it's archived, so I guess there's really no noticeable impact.

----

**I need a dialog to know which document it is dealing with so it can use the model data associated with that document**

I have a multi-doc app that calls for one or more (almost completely read-only) document view windows that show an image.  To edit the properties of the underlying model, I'm trying to figure out how to present a modeless dialog box with controls that manage the appearance of the viewed object.  When the user switches between open documents, the modeless dialog box automatically updates to the settings of the (object represented) by the current document.  The closest examples I can think of is OmniGraffle or Photoshop where you can have one or more open documents, and floating windows controlling each one. 

----

This is a fairly common question and there are several different approaches to the problem. Here are a couple pages that might help: MakingNibsTalkToEachOther and HasPantherChangedHowToDoNibs. The pages are kind of a mess however, so does someone want to create a well refactored page on the best way to do this?

---- 

I'm still missing some key concepts. Can you provide more detail?

----

From the Document Model glossary, with my comments:

An NSDocumentController owns as many NSDocument objects as necessary for the currently running application.  NSDocumentController very rarely needs to be subclassed.

So no brainer -- NSApplication (or MainMenu.nib) owns my non-customized NSDocument object.  I imagine internally there's just an NSMutableArray (or similar container) maintaining the NSDocuments.

The shared NSDocumentController would own your multiple-document object. your pallette window would exist outside of the document architecture and simply pass messages to     [[NSDocumentController sharedDocumentController] currentDocument]

Each NSDocument owns one or more NSWindowController objects.  Document based apps must have at least one NSDocument subclass.

----

I'm going to have multiple objects, and each instance will presumably have two NSWindowCrontroller instances -- one for the view window, and one (pointing to a shared?) NSWindowController object.  Or at least that's one way I'm thinking it could be done.

----

Each NSWindowController owns a single NSWindow.  NSWindowController objects are frequently subclassed, except in extremely simple apps.

Say FOOViewerWindowController owns the one-per-document window, while FOOEditorWindowController owns one window.  I think this is the trick -- to get no more than one instance of this second class, FOOEditorWindowController, instantiated at any given time.  Then maybe some delegate (on FOOViewerWindowController?) would call [FOOEditorWindowController setDocument:[self document] every time the key window changes?

I'm surprised that there aren't more examples of this, given the large amount of multiple-document applications there are that use this system.  Photoshop, OmniGraffle, Interface Builder, MS Word, just to name a few.  Although again, I may just be missing some key concept that's so obvious it doesn't need to be exemplified. 

You're on the right track (or have already finished the race. To change your pallette window based on the frontmost document, you could post your own NSNotification from     windowDidBecomeMain (NSWindow delegate message... your NSDocument class must be its window's delegate to do it this way). Then just register for the notification in your pallette window's window controller. You can use a     userInfo dictionary in the notification to pass whatever values you need to set in the pallette.

----

**The "Save" menu item sends saveDocument: through the responder chain by default**

I'm trying to figure out how to catch Cmd-S in other windows belonging to NSDocument. I have a 'project' as the document and a 'note' that loads its own nib with its own controller. I want to be able to save the whole project when I press Cmd-S while the note editor has focus. What is the best way to do this?

----

The save menu item sends a     saveDocument: action through the responder chain by default. So just implement     -(void)saveDocument:(id)sender in your class.

----

I added the following:

    

- (IBAction)saveDocument:(id)sender
{
  self ownerDocument] saveDocument:sender]; // sender could be self too, but ... meh.
}



The controller class already gets its "ownerDocument" set for many reasons, so this was fairly straightforward.

----

If you need to do this for other stuff as well, you might want to just have your existing [[NSDocument class in the responder chain for your note editor, and not respond to saveDocument:

