---
layout: page
title: CoreDataWithDefaultObjects
---

Hi yal!

Im creating an app that utilizes CoreData for the structure, but I need to create 5 objects at the "first startup" eg when theres no XML file yet.
I�m using a Finder look-alike view and want to create the default buttons, and after those I want to be able to create new Folder beneath.
The 5 objects have custom settings from the NSManagedObjects that is the Folders, so I need create and edit them before I insert them into the tableview.

Any ideas on how to do this?

----

Well ... how about creating the objects? :-) Seriously, it's that straightforward. The piece I believe you're missing is 'where/when' to create them. In the case of a document-based (NSPersistentDocument) app, you'd want to do it somewhere in your document's init method (whichever one you're using - I use initWithType:error:). In the case of a non-doc-based application, you could do this in your app delegate's applicationDidFinishLaunching: method (after checking the store for a flag that says something like "firstRunInitiated"). In this case, you would want to store it in your store's metadata. 

Read the Core Data Programming Guide ( http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/index.html ) for information on how to programmatically add managed objects to your context.

----

Please read the documentation. This is explained explicitly in http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/04_Department/chapter_5_section_2.html.
If you are using NSPersistentDocument (or indeed NSDocument), there is no need to set any "firstRunInitiated" flags -- simply implement initWithType:error: (which is called "only when a new document is created, not when it is subsequently reopened").

----

Please read the post to which you are replying. :-) I gave two separate explanations (one for doc-based and one for doc-less applications). The former needs no 'first-run' flag. The latter does (unless you look for the existence of the store itself, which isn't exactly the same, especially if it's an XML store that can be edited (and emptied) by another app).

----

The documentation covers both scenarios -- see the Core Data FAQ "How do I initialize a store with default data?": http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html

----

Thanx mate, this solved my problems :)
I actually did create my objects but I didi it before the NSArrayController had loaded yet... Thanx!

----

That shouldn't be a problem. The NSArrayController is just performing a fetch against the NSManagedObjectContext that it's bound to.  If managed objects can be returned from that fetch, the array controller should reference them and whatever view is bound via the array controller should display them.

