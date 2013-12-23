---
layout: page
title: DocumentModalSheetAndInspector
---



I have a document based application I'm working on, with a document window and a shared inspector panel (singleton window controller) that allows the user to change attributes for the document. When the user changes an attribute, I begin a document modal sheet that prompts for a comment to go along with the change. When this happens, the inspector panel obviously needs to be disabled. The inspector can't be part of the modal session however, since the user should be able to switch to a different document and still use be able to use it.

So, does anyone know of an elegant way to disable to the inspector? My only idea at this point is to register for NSWindowWillBeginSheetNotification, check if it's the main window, and disable each widget (either through code or by binding them to some object that's keeping track), then do the same when the sheet ends or the user switches to a new window. This seems like a lot of work though, and I was wondering if anyone had any ideas for a better solution... maybe there is none?

