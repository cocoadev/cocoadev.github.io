---
layout: page
title: MyDocumentFromCurrentDocument
---

I need to access the current myDocument NSDocument subclass in a Cocoa multi-document application. What I'm doing is casting the NSDocument * to a myDocument * like this:

    
NSDocument * theDocument = [[NSDocumentController sharedDocumentController] currentDocument];
myDocument * myDoc = theDocument;


This works but the compiler issues a warning: warning: initialization from distinct Objective C type. Should I be doing this some other way?

If no document is open, theDocument will be nil. Then I:

    
myDocument * myDoc = [[NSDocumentController sharedDocumentController] openUntitledDocumentAndDisplay: NO error: &outError];
[[NSDocumentController sharedDocumentController] addDocument: myDoc];
[myDoc makeWindowControllers];
[myDoc showWindows];


The created document will by a myDocument *.

----
This is acceptable practice if you *know* that all of your documents will be instances of myDocument (and by the way that should probably be M<nowiki/>yDocument just to follow standard naming practices). The compiler warning tells you that you're only guaranteed an NSDocument; to tell it that you understand the problem, use a cast.

    
myDocument *myDoc = (myDocument *) [[NSDocumentController sharedDocumentController] currentDocument];

There still might be a better solution, though; for example if you're trying to get the current document from an object instantiated in your document's nib file, you should probably just use an outlet and be done with it. I'd only recommend this code if you had an application-wide controller that needs to access the current document. --JediKnil

----
I knew I could use the cast to surpress the compiler warning. I just wondered if Cocoa had some object oriented way to do this that I hadn't found in the documentation. This is in some code for a panel controller so yes, it's in an application-wide controller.  Thanks for your help.

----
This sounds like an instance where you could be using the responder chain.  Typically since panels affect whatever the "currently selected object" is in your app, you would just send a message down the responder chain, and the appropriate document will pick it up.  That's why panels never become the main window, so that the current document's window is still main.

If for some reason your panel doesn't work that way, you could always send the message programatically (i.e. an informal protocol).
----
Forgive me if this is already obvious to you, but (depending on what you're doing) you may not even need a MyDocument pointer at all. The code you show just calls -makeWindowControllers and -showWindows, which are both declared in NSDocument. If these are all you're calling, then an NSDocument pointer is all you need -- if you override these methods, your versions will be called even though the type of your pointer is NSDocument*.

There's no problem casting an NSDocument* to a MyDocument* as long as you know that the only documents in your app are MyDocuments. In the long run, though, life is usually nicer if you don't have to do that. If you're making this cast very often, it may be an indication that you're subverting Cocoa's document architecture. That may be fine if you know what you're doing, but it's surely a recipe for nonstandard behavior and added complexity. It's also likely that your MyDocument class is acting in a view-controller capacity rather than NSDocument's intended model-controller, so you might consider factoring your code differently. -CS

