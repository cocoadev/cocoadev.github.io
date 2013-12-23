---
layout: page
title: DocumentBasedInteractionWithOtherElements
---

I have a design related question here.

I have a document based app.  I have a textview (where the editing takes place).  The textview is a custom one with a method that calls an NSLog (for testing).  I register for the textDidChange notification of the text storage inside the document subclass and call the method in the text view whenever the user modifies the text:

(inside the windowControllerDidLoadNib method)
    
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(processEditing:)
name: NSTextStorageDidProcessEditingNotification
object: [textView textStorage]];


Problem is, the notification is never being sent.  Is there something special that needs to be done because this is document based?  This notification works fine in a normal one window app.  I am also using a custom window controller for a toolbar as documented in the second part of ProperWayToUseNSToolbarInDocumentApps.  Thanks a bunch for any help you can provide.

----

Does it work without your window controller class?

*If it does, you could try overriding     windowDidLoad in your window controller subclass to call the method yourself. Also, NSDocument's documentation specifically says that the notification will only be posted if the document object is the nib file's owner...and not the window controller. --JediKnil*

----

Well the notification works if I take out the window controller class, thanks there.  But now my problem is that the toolbar doesn't work WITHOUT the window controller class.  I followed the example in ProperWayToUseNSToolbarInDocumentApps and met with no success (including when creating new projects and starting from scratch just to test the toolbar).  Is there some extra, blatantly obvious, step I am missing in the process?  Thanks for any help�

*Gr... got it working.  Thanks*

