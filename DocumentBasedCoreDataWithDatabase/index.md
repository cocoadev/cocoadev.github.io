---
layout: page
title: DocumentBasedCoreDataWithDatabase
---



Hi all!
Im writing a small app just to learn CoreData and I've run into some trouble

See Im trying to do a Document-based CoreData app, but I want to have a "database" with objects that the documents use.
So when I load up my program I want to load this database (i.e ~/Library/Application Support/MyApp/...) and then when I load the document-file
I want it to go through the document-items and update them from the database...
I believe this can be done with fetchRequests but since Im a newbie at coredata someone could maybe push me in the right direction?

Ok, so to be clear:
How can I have a document-based-CoreData object that uses items from my other CoreData-database?
I want to be able to update the database and automatically all my documents created in the app uses the new items in db! (when the document is loaded)


----
Would you not create a framework for your core data source which would then supply methods returning the context for that store to the receiver. It seems like this is well within the lines that core data was meant to fulfill, and most likely how you would implement in a given application anyhow; creating a framework or isolated code portions for your model and controller, leaving you free to reimplement as you see fit. Additionally, keep in mind that these files are all written with the same mechanisms, so it seems likely that you could simply reimplement your data model in another app and be "off to them races"

I've just started working with Core Data honestly, so I could be totally wrong. This does seem however, to be the way in which the system was designed to be utilized.

TomIngham
----

