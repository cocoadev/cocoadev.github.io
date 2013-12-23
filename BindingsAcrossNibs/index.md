---
layout: page
title: BindingsAcrossNibs
---

Is there a simple way to use bindings across two or more nibs? In my particular case, I have MyDocument.nib and MainMenu.nib. The main nib will have an inspector for the selected objects in a table view in the active document window. How can I use bindings with that inspector and document? Are there any examples of this? Apple encourages the use of inspector panels, so I would think that they've developed a way to do this.

----
Bind to something like NSApp.mainWindow.windowController.document.whatever, or possibly something starting off NSDocumentController.
----

Any examples of it? How do we bind through NSApp in the main nib? File's owner?

----
Yes, or Shared Application in the popup.

----
I just tried this, and the problem that I have is that MainMenu.nib loses the connection with the managedObjectContext of the current document if the document loses key, and the user then clicks on that document window again.

----
Upon further testing, the above technique does NOT work well. For whatever reason, it will lose the managed object context binding if the app or current document loses focus when you reselect the document again, until you select a different item in your array controller. If anyone can figure out another method, post here. I'm going to experiment and see if I can figure out what the issue is and find another method that works.

----
    mainWindow is nil while your app is in the foreground. Stupid, maybe, but it's right there in the docs.

As a workaround, you can write a method into your application delegate (or some other convenient location) which returns the current document. Then you can bind to     NSApp.delegate.currentDocument.whatever. You'll have to make sure that you post the appropriate KVO notifications from the delegate for this to work.

----

Do you mean [[NSDocumentController sharedDocumentController] currentDocument}? How do I use KVO on that?

