---
layout: page
title: PredicateFilterDoesntWorkForMe
---



Hi,

I have a simple project, I want to change the predicate filter for an NSArrayController at run time. As part of my initial testing however, I tried just to hard code a simple filter     title == "foo" in my ArrayController in Interface Builder.

I have two ArrayControllers hooked up to the same managedObjectContext - one has this filter predicate, the other has no predicate applied. All the bindings were set up by dragging from XCode's modelling view. There is one entity, with one attibute, '    title'.

But the filter predicate appears to have no effect whatsoever, all records are returned. This is very baffling to me - new to predicates in Core Data, and they don't behave as I'd like them to :(

Here is the *very* simple project file: Connect to my iDisk public folder, username 'peterlaurens'.

Help much appreciated,

- Peter

----

Try entering a few test "Thing"s into your store, naming them randomly, then create one named "foo". Press the Fetch button. The non-foo Things should disappear, leaving only your foo Things. Newly-inserted objects appear to be excluded from the filter, which makes sense if you think about it. If you manually set your filter predicate and the user creates a new (non-matching) object, they'll just keep clicking that 'new' button because they don't see anything showing up. If you save this document and re-open it, only the foo objects are shown, since the non-foos aren't newly-inserted. Also, if you create a new Thing (and you have its initial title set to "New Thing"), then you edit its title to something not 'foo', it'll disappear as soon as you end the editing.

----

Thanks, (+ apologies for double posting to Cocoa dev list.)

So if I want an nsarraycontroller to be very strict about such things (never appear to breach the predicate) I should force a fetch after each insert?

*Try it and find out. :-) It's a good learning process. Using -fetch: just refreshes what the controller represents, so yes that would have the effect you're looking for.*

----

*Could you change the predicate?*

