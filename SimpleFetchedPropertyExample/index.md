---
layout: page
title: SimpleFetchedPropertyExample
---



Hi all,

been playing with Core Data, and quite simply, can't figure out how to make my Fetched Property fields work, I have two Entities, not linked, and I want one to say, select * Entities of the other type where its 'Title' attibute is, for this example, 'Foo'.

And then I want to display a list of these titles via bindings in a tableview. I just can't seem to set everything up properly... so I wonder if there's a (very simple - i.e. *not* Core Recipes ;) ) example anyone has.

Have been over and over the documentation, not helping either.

Sure it must be something very simple I'm understanding wrong.

Thanks for any help.

- Pete

----

If you want to fetch a list of entity instances where the title == foo, you shouldn't be creating a fetchedProperty on the other object. Unless there's something more to the model that you're not mentioning, this is a very long way around. ;-) To me it seems like taking the face-plate off a wall outlet, then pulling a few feet of electrical cord out to move the plug closer to the appliance, rather than just moving the appliance (or using an extension cord). :-D All you need to do is set up an array controller in your nib, bind it to the managedObjectContext, and set its represented type to Entity rather than Class. You can then type in its filter predicate box     title == foo or     title like[c] foo for near-matches. Bind your table columns to the array controller and viola! Of course you don't have to hard-code the filter predicate; you can bind it to some property of some other object ... but all of this is in the docs; it's how I learned. Look for the Core Data Programming Guide and the Predicates Programming Guide in the docs. They're most helpful.

----

Ah now that does sound like an entirely fresh approach to what i've been monkeying with... I'll certainly play with that, and perhaps return to understanding a fetched property field on an Entity a little later.

[...5 mins later...]

The model I'm putting together is for SmartList capability, so I have a SmartGroup entity, and a proper 'thing' entity (call it whatever you like, it only has a 'title' at the moment).

So every time someone selects a new SmartGroup item I will need to change the predicate (is that via binding?) of my ArrayController... does this still sound like the route to go down?

Thanks

----

I don't think there's enough information here to determine that for your particular project, but ... From the perspective of the SmartGroup item, that's all it *is* is a filter with a pretty name. If SmartGroup entity can contain many Thing entity instances, then *one* way to do it would be to create a new property for SmartGroup called     filterPredicate. The SmartGroup would remember the predicate it uses to 'be smart'. Via bindings, the array controller can have its filterPredicate binding bound to the SmartGroup's filterPredicate.When the array controller is  set to use the Foo smart group, the 'look for foo' filter predicate is automatically loaded and that's that. You don't have to manually set the filter predicate; that's what Bindings are for. Of course it's up to *you* to implement the mechanism that allows users to specify the filter predicate to be stored in the SmartGroup. **Update:** Since NSCompoundPredicate is a subclass of NSPredicate, a Smart Group with more than one criterion (what users would expect) should use an NSCompoundPredicate instead of an NSPredicate.

----

What type would you recommend the property be? I'd guess either a fetched property or string... 

----

