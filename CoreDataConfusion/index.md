---
layout: page
title: CoreDataConfusion
---

trying to run down a problem I'm having with core data. I can easily make a "to many" relationship work. everything. it just works like magic.  but just try making a root level entity with one attribute, and it never works.  I can't find any documentation, or tutorials... what gives? doesn't ANYBODY control their interface with core data? 

heres how to duplicate the problem:

make a NEW core data document based project in Xcode.
in the model graph, make one entity, with one attribute, say an Int value.
open the myDocument in IB, and switch to Xcode, keeping the IB window visible... option drag the entity to your window (no more screwing around... core data drag n drop!)  select the option for one object.

now save and build the app.
running it will give you a grayed out control, that says: "no selection"  well of course there is no selection. theres no array or Group of objects to make a selection from, so theres NO NEED for a selection.  its driving me insane, because this exact tutorial allows you to create massively complicated and detailed hierarchies of data.  But it fails miserably at allowing you to make a flat list of attributes ideal for controlling an interface on a document to document basis.

I'd love to hear a fix, but I'd like it even better if someone could point out a spot in the docs that explain exactly what the binding panel expects from a core data model graph.  Why isn't anything in the docs connecting any of the dots here? 

----

I am not able to try your example at the momemt, but it sounds like your issue is more with CocoaBindings rather than CoreData:
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CommunicatingWithObjects/chapter_6_section_5.html

Try applying these principles without resorting to the drag and drop.

----

Just because you've generated a single-instance interface doesn't mean that there's an instance of your entity created for the interface to be connected to.  You'll need to create an instance of your entity in code, bind the content of the generated NSObjectController to that instance in order to work with it.
----

As the documentation makes abundantly clear, Core Data is a model-layer technology, not a view-layer technology.

The not-exactly-difficult-to-find NSPersistentDocument Core Data Tutorial (http://developer.apple.com/documentation/Cocoa/Conceptual/NSPersistentDocumentTutorial/index.html) makes it clear how you can initialize a data store and set up an NSObjectController to do almost exactly what you appear to want to do.  See also "How do I initialize a store with default data?" in the Core Data FAQ at http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html.

