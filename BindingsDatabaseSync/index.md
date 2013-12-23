---
layout: page
title: BindingsDatabaseSync
---

Any thoughts on techniques to sync the Cocoa Bindings and a Database like sqlite? 

----

I am looking at DataCrux by Scott Stevenson at Tree House Ideas.
http://treehouseideas.com/

Just started experimenting, so no info yet. Don't know if it will work or not.

PhilipRiggs

----

I'm still waiting for the next release of DataCrux. Supposedly there are a lot of changes in the works.

----

DataCrux seems a lot like CoreData, all the way down to using SQLite. Does Scott Stevenson work for Apple?

----

No, I don't. :) I think this is one of those situations where both Apple and I arrived at the same logical conclusion independently. That said, DataCrux isn't a clone of Core Data or vice-versa. They address the same general area, but there are differences in strategies. The most significant difference is that DataCrux apps can be deployed on 10.2. LogTen ( http://coradine.com/software/logten.html ) is the first commercial app to ship that uses DataCrux. The public version of the build that LogTen uses is nearly ready.

----

There is now QuickLite 1.5. I've had good experiences with QuickLite 1.0. Version 1.5 looks even better.

