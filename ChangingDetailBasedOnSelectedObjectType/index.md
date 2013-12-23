---
layout: page
title: ChangingDetailBasedOnSelectedObjectType
---



I'm creating what is hopefully a simple testing application (Core Data document app).  I have a simple interface so far (although I am not opposed to trashing it and starting over if I've eliminated my options).

It's a basic master/detail view with the details relevant only to the current testing type (web requests/content match).  I'd like to add the ability to test other components without writing a separate app (or window) for each.  For example, a sql query with an expected result.

I thought about doing a tab view, but that seems wrong, in that all the data is still populated.  I just want the test-type-related information published without created a monster denormalized object with "name, type, webaddress, webexpectedresult, sqlquery, sqlexpectedresult" etc. fields.

If you've seen an example with source, I'm more than happy to dig into that as well.

----

You might need to break it down further.  You're not thinking about your problem in Cocoa terms yet.  In Cocoa terms, it sounds like you want to show a different detail view for each type of Core Data entity, which means swapping between NSViews.  Time to put together some key concepts:

1) [Core Data concept] Every Core Data entity has a type, which has an associated class -- by default, it's NSManagedObject, but you can specify whatever NSManagedObject subclass you like.
2) [AppKit concept] An NSView can contain other NSViews, and can be inserted into and removed from an NSWindow's view hierarchy.
3) [Cocoa concept] You have some NSObjectController instance in your nib (probably an NSArray controller) that maintains a selection.  NSObjectController exposes a "selection" binding, which means that you can register for KeyValueObserving notifications on it.

So what does this all mean?  When your selection changes (3), you want to swap out whatever detail view is currently being shown for the appropriate detail view for that entity type (2).  You can get this view from the entity type's object (1) -- your custom NSManagedObject subclass could have a class method that returns an instance of NSView (or maybe NSViewController on Leopard, since this is the exact circumstance that lends itself to using it) with appropriate bindings already set up.  You could even load this view (or view controller) from a nib at runtime.

Hope that gets you on the right track.  --K

