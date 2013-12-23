---
layout: page
title: NSObjectControllerSelection
---

I'm trying to create a multiple-document-type CoreData application.  There are two NSPersistentDocument types; one type (Foo) will be automatically loaded at program start (set in the user defaults), and the other (Bar) will be loaded via the "Open..." in the menu bar.  I've been through all the tutorials and just can't quite figure this one out.

The first document type, Foo, has an entity (Baz) which is a singleton.  That is, there is only one Baz per Foo.  Baz, and consequently Foo, must be available to its own window(s) as well as the window(s) mainly used by Bar.  I'm not really concerned about Bar right now.

I have one nib which I want to use to configure the properties of Baz.  I've modified Foo's makeWindowControllers: method to create an appropriate BazController, as well as other necessary NSWindowControllers for the other windows that will use Foo.  The nib that I want to configure Baz with has an owner of BazController, and an NSObjectController instance.  The NSObjectController's mode is Entity and the Entity is Baz.  Several NSTextFields in the nib's window are mapped to the NSObjectController's selection: method and the appropriate properties of Baz.

I can add an NSButton to the window which triggers the NSObjectController's add: method, and the NSTextFields are initialized and set to their defaults.  I assume that means if I execute the add: method, then the "selection" is updated.

However, since Baz is a singleton, and an empty Baz is created in Foo's initialization, I want the nib to default to this empty Baz.  Currently when I launch the window, I see "No Selection" in the NSTextFields.  I don't want to have to use a button to "add" a Baz, because there should only be one, and it is created by at document initialization.  How can I set the selection of the NSObjectController manually?  Should I even be using an NSObjectController here?  Should I be creating Baz somewhere other than Foo's initWithType: method?

Thanks.

----

Using an NSObjectController for that is just fine.  I think the disconnect is that you're looking for a way to set the     selection when it's really the     content of the NSObjectController that you want to set.  A controller's     content is an input, while its     selection is an output; a useful mnemonic for this is to think of them alphabetically.

Also, be sure that you're creating a single instance of Baz in your Foo document (e.g. do a fetch first, and only create a new one if you don't get a result). When the BazWindowController's nib is loaded you can then set the     content of the NSObjectController to your Baz.

You should also be able to bind your NSObjectController's     content to a key path that points to the document property containing your Foo instance, by specifying a key path that's bound through the BazWindowController's     document key in Interface Builder.  (Your BazWindowController's     document property will be set appropriately when you add it as a window controller in your override of     -[NSDocument makeWindowControllers].)

----

Cool, that worked.  I created the NSObjectController in the proper nib, but left its bindings empty.  Then, when creating a new Foo, I called its     makeWindowControllers method, then the object controller's     setManagedObjectContext, and finally the object controller's     setContent.

----

You should not be sending     -makeWindowControllers to your FooDocument instance.  When it's created, the framework should take care of that.  And if you have your NSObjectController instantiated in the correct nib, you should be able to do everything you need using bindings; you shouldn't have to send your object controller either     -setManagedObjectContext: or     -setContent:.

(Some Objective-C notes:  You send messages to objects in Objective-C, and the runtime calls methods on your behalf; you don't call methods.  Also, the colons are part of the message selector, not separate from it, so it's important to put them in when discussing messages and methods.  And finally, as a general suggestion, it's important to give even example classes descriptive names, such as "FooDocument" rather than "Foo".)

