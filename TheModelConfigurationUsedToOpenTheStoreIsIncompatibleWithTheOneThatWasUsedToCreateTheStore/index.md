---
layout: page
title: TheModelConfigurationUsedToOpenTheStoreIsIncompatibleWithTheOneThatWasUsedToCreateTheStore
---

This error appears on the General/CoreDataErrorsIveCaused page, but I wanted to pose another case and see if anybody might have an idea for how to fix it.

I had a core data application using a single sqlite datastore from a single model in 10.4.  To my dismay, when upgrading to 10.5 - no changes to the app - the user receives the error for which the page is named, and can never again retrieve that data.

I've found many resources for this problem on Google, but all of them fall into one of these three groups:
1) I'm just testing so by deleting the store and starting over I fixed the problem
2) Make sure your model is the same as the model you're opening, accounting for spelling, abstract objects, relationships, etc.
3) Read up on migration and make sure your process is right.

The original model did not have a version number, but according to the docs that's just a "hint" anyway, essentially ignored by core data.  Something changed in the structure between 10.4 and 10.5 which made the existing store incompatible with its own application, with no changes to either (just the OS).

I did the sensible thing, and tried several strategies (I still have the original code and dataset and have numerous copies of them to experiment with):
- Simply recompiling the app in 10.5 and feeding it the datastore.  No go.
- Automatic version migration by defining a (identical except in version number) new model and a mapping model and the automatic migration algorithm.  I can verify that the process executes it just doesn't work.
- Manual migration using the same models and migration mapping models made explicit.  This gives the error explicitely in the userInfo dictionary.

So let me reiterate: the Model has not changed at all, in the app, in the datastore, or in the core data model diagram.  There's most likely some way that Core Data is determining if a model is suitable for a store and it can't make that assertion in this case - I think this is the problem (something to do with the lack of version numbers?)

Please, does anyone know how this might have gone wrong or what I could do to get the data recoverable?  I'd like to release a new version of my app which can still read data from previous versions!
