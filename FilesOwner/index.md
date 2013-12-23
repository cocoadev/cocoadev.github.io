---
layout: page
title: FilesOwner
---



To quote Bill Cheeseman:

The File's Owner icon is a "proxy" for whatever object owns the nib file representing the [...] window. A proxy is used because the nib file's owner can never be instantiated in the nib file; the owning object must already exist in memory before the nib can be loaded. The owner is in this sense external to the document's main window, but a means of communication between the owner and the window must be provided. The File's Owner stands in for the owning object, to enable you to draw the necessary connections between it and other objects.

(Source: http://www.stepwise.com/Articles/VermontRecipes/recipe01/recipe01_step02_05.html#note01)

----

In essence, whichever object loaded the NibFile becomes the file's owner for that nib file.  You can make connections to it in InterfaceBuilder, even though IB doesn't know exactly where or what the file's owner will be when the code is actually executing. 

----

(Please correct as needed - this should be basically right, but I might have gotten some of the details wrong. Thanks.)

The FilesOwner is a concept you'll need to understand anytime you need more than one NibFile in your application, bundle, et al.

Imagine this: 
You have a NibFile that contains the layout for your document. You also have AnObject as a controller for each of your documents that gets your data into the document. According to the ModelViewController paradigm, the controller will get its data from someplace else, but that doesn't concern us here.

The important thing is: For each document window we have an instance of the corresponding controller object connecting controls to an ActionMethod and / or an OutletVariable. (Actually, several actions and outlets, but Wiki links make plural forms difficult... :) )

Now here's the problem: Usually when we work in InterfaceBuilder, we instantiate class files, then connect actions to controls etc. We cannot do this in a DocumentBasedApplication, because the document controller object and the document window are to be instantiated at runtime, whenever the user chooses "File->New" from the menu bar. There might be many open documents ( = instances of document window and controller object) at once or none at all. We don't know ahead.

But without an instance of the object, we cannot connect outlets and actions, right?

Wrong. In InterfaceBuilder, you can set the FilesOwner property for the NibFile. Set it to the class that owns the document window and you'll have everything necessary to connect outlets and actions. The "instance" you connect to is the FilesOwner icon in the Instances tab in InterfaceBuilder.

----

The concept of a FilesOwner is merely a convenient abstraction. In reality, I think it works like this:

A NIB file has two catalogs. One catalog lists NIB object/ivar pairs; it records which NIB objects are connected to which File's Owner outlets. The other catalog lists NIB object/selector pairs; it records which NIB objects target which File's Owner actions.

NSBundle's loadNib... methods get passed an owner object. At that time, NSBundle scans the catalogs, the owner object's ivars, and the owner object's methods.


* If one of the owner's ivars matches an ivar in the outlet catalog, NSBundle sets the owner's variable to the corresponding NIB object.
* If the owner responds to a selector listed in the action catalog, NSBundle sets the corresponding NIB object's "target" and "action" variables to the owner and the selector.


These are the only connections made, and they are one-way connections. The NIB objects don't know about the owner except as a target for action messages. In other words, they don't have an "owner" member variable. And the owner object doesn't know about the NIB objects unless NSBundle assigns one to an outlet. It's a loose coupling. Once I understood this concept, NIBs made a whole lot more sense.

If you are constructing a document-based application, you may want to read:

ConnectNSDocumentSubclassOutletsViaFilesOwner

