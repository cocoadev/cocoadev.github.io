---
layout: page
title: NSDocumentAndNSWindowController
---

I have a document-based app that is not using NSDocument subclasses (basically writing all the MDI support myself).

I got bitten because I added a document getter to my window controller. Apparently the window calls something like

    [self windowController] document] isDocumentEdited]

although the     document message in the window controller class is said only to return     id. The result is used for handling the responder chain (i.e. sending a message to nil will reach the document of the current window, if no-one else responds to the message. This should *not* require that the instance be (a subclass of) [[NSDocument).

Can anyone give more details about what is really required by the document returned? I would prefer to use the name of     document for my getter, and I could just implement     isDocumentEdited This seems quite risky, since others might perhaps also make unjustified assumptions about the type of the result.

