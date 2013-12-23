---
layout: page
title: CoreDataAndPreferences
---



Hi friends,
I have core data app, it can store lots of entity's instances through array controller. I would like to save the preferences in the same persistent data store, so I used object controller since the preferences are the same for all items therefore no need for another array controller.
But it does not work - I even can't type into bound preferences' text field. What am I missing?

----

What is your controller bound to? Do you have a "Preferences" entity or something like that? If so, you need to create an instance of that entity when your new document is created (or at app launch in a non-document-based app if an instance doesn't already exist). Bind the controller's content to that instance and it should work just fine.

----

Thanks for reply! Unfortunately, I'm affraid I don't really understand. Yes, I have "preferences" entity. Then, I have instance of object controller with managedObjectContext bound to AppDelegate with key path managedObjectContext. And yes, it is non-document-based app. And there is a text field which should serve as input to one of "preferences"'s attribute. But I don't really know what to do next. My object controller doesn't have (or better said, doesn't show in IB) content. And how to create an instance of "preferences" entity? I don't see it anywhere... Sorry, for such a newbie questions.

*I see. Your immediate problem isn't really Core Data-related. It's Bindings-related (where you're storing that information - Core Data - is irrelevant for now). You'll need to read some more on Bindings (this information is everywhere, so it's a waste of time to repeat it here). You'll want to check out (on this site) CocoaBindings and also read the excellent Bindings-related articles on Cocoa Dev Central: http://www.cocoadevcentral.com/   ... That should get you going.*

