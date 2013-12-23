---
layout: page
title: OnlyMainMenuNibLoadsDocumentNibFailsToLoad
---



see also CantCreateNewDocument

I'm attempting to add a document based Cocoa app to an already existing Xcode project. My source compiles and links correctly, and the product app launches, but only the MainMenu.nib loads. The window for the document doesn't load, even when going to File->New. I suspect there's some setting I missed somewhere. Both nib files are included in the project and assigned to the proper target. 

----

What I'm guessing you didn't do is add a document type to your Info.plist (available by choosing Get Info on your target and choosing the Properties tab in XCode) that pointed to your NSDocument subclass; that's how the app knows what NSDocument subclass to use when opening or creating documents.  -- Bo

