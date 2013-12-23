---
layout: page
title: NewNSDocumentTypes
---

I have a working document-based app which handles the viewing and editing of two different ( but related ) document types. Both are registered to the app and in both cases the correct nib file is loaded. 

The issue is that when I click File->New... the app makes a new document of the first type. But what if I want to make a new document of the second type, or maybe a third in the future?

As I see it there are two options. 1) There could be a way I'm unaware of to make the File->New... menu auto-populate with each document type. or 2) I manually create a sub-menu with one entry for each document type.

Option 1 would be fantastic. 

On the other hand, if I need to go for option 2, what message would I have to send, and to whom, to signal to create a new document of some type? I'm a little unclear on document-based programming, as quite a bit of it seems to be magic behind the curtain.

--ShamylZakariya

----

There is unfortunately no way to have the New menu populated automatically. Probably it would also rarely be useful, since e.g. TextEdit can open Word files, but New should not give you that choice ;)

To create a document of the desired type, send     openUntitledDocumentOfType:display: to the shared document controller.

By default the document controller will create an instance of the first document type listed in Info.plist

See NSDocumentController for more info (but yes, much does seem to be magic).

