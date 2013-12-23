---
layout: page
title: MultipleDocumentTypes
---



I'm interested in creating a sort of map building application.  Basically it will have a height map document (ie a gif), a terrain map document (ie a jpeg), and a master application document to keep track of the former two.  When the user opens a master document, a new UI opens with it's height and terrain maps loaded too.  When the user opens a height or terrain map document, that document is loaded into the current master document.  This is where my problem is.

When one of the slave documents is opened, a new master document is created and a new UI pops up.  Like I said, I want an opened slave document to load into the current master document.  I've tried messing around with a subclass of NSDocumentController.  That didn't go so well.  Are there any tutorials out there that go over something like this?  Any help is much appreciated!

Thanks,
Mark

----

There may be a better approach for what you want to do, but the way I would approach the problem would be to include the image maps in your main NSDocument subclass and its data file. Instead of trying to open a new document when the user opens an image, provide an Import Map... menu item that will load the image within your main NSDocument.  Your NSDocument subclass should contain window controllers for each window that handles each kind of map. If you want to allow the user to have multiple map images in each NSDocument, create a new instance of the appropriate window controller when they import a new map. You can also make the window controller a singleton that's shared between documents, if you want a window to be shared between all open documents (such as an inspector window).

If the code to handle the different map images is substantial, you might also want to create model classes for them. Again, it would be up to your NSDocument subclass to keep track of these.

You could also implement the NSApplication delegate method that handles opening a new document, if you want the Open... menu item to handle both maps and documents, although in my mind this wouldn't be a good UI choice. Let me know if you'd like any more information, and feel free to clarify if there's something else you need to do that doesn't fit in the above strategies.

-DF

----

Thanks!  I'll try it with the Import Map menu item suggestion.  That seems to be fairly straightforward.  After thinking about it a bit, I tend to agree having the Open menu item handle everything might be a little confusing.

Mark

