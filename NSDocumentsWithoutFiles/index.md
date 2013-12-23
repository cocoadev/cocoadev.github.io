---
layout: page
title: NSDocumentsWithoutFiles
---



Is there any good way to open & save documents that aren't list a traditional file?

I'd like to have an app that reads and writes internet-based non-HTML data directly, and translate it to editing in a Cocoa app. Saving will just write it back out, and opening should just load it from the proper location on the net. I still want all the stuff     NSDocument gives me for free, like Undo support, dirtying documents, etc. Basically, I just want to override how an     NSDocument loads and stores documents, not just the format but also the method used.

I'm planning on overriding     saveDocument: in     NSDocument, and     newDocument:,     openDocument:, and     openDocumentWithContentsOfURL: of     NSDocumentController.

What I want to know is: (1) What should I override to do this? (2) Is this a good idea, or is there something I'm overlooking in the design?
--Mel

----

I would try overriding     -readFromURL:ofType:error: and     -writeToURL:ofType:error: ... This is a good place to get the direct contents of the online resource and push it back again. As to whether or not it's a good idea, that's impossible to say without knowing more about what you're trying to do and the type of data you're trying to edit. Also, it'd be helpful to know by what transport method (HTTP? FTP?) you're going to be up/downloading the data.

It would also probably be a good idea to provide some way of saving the document data locally in the event that the resource becomes unavailable between reading and writing.

----

The online data will be downloaded in a custom manner -- I won't be able to use URLs in the app. I'm currently spawning an NSTask to do the work with a command line tool, and just reading stdout into an NSData. I want to change the current half-written app to be an NSDocument-based one, because I realized that I could get a lot of behavior for free with that.

So, to be more clear, **I don't want the open panel or save panel to ever appear** (bold for emphasis). I'm setting the information I need to retrieve the data in the application preferences. Then, the app will put together the arguments for the NSTask, and retrieve the data. After editing, the data will once again use an NSTask to send the data back out.

----

-newDocument:, -openDocument:, etc. in NSDocumentController are just actions that are supposed to be triggered by corresponding menu items in your UI. You can override them to do whatever you like, or you can implement your own -(IBAction)openDocumentFromSecretServer:(id)sender and have your menu item trigger that instead. Same goes for -saveDocument:... you can override it, or you can ignore it and hook your Save command up to some other action of your own creation. Sounds like you're on the right track to do what you say you want to do (even if that sounds a little weird to those of us who don't know what it is that you're building). -CS

----

Thanks. When you put it that way, it sounds so simple. Just use my own methods, and don't worry about it. I was so set on subclassing NSDocument/Controller that I missed that.

