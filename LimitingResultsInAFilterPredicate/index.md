---
layout: page
title: LimitingResultsInAFilterPredicate
---



Using CoreData, I'm modelling the recent changes to Wikipedia. I have 3 entities: Articles, Users and Edits. Thing is, I want to only show the top 10 edits that match a certain criteria. I've set the filterPredicate on my NSArrayController to filter based on my criteria, but I'm unsure as to how to go about limiting the number of results. I know it's possible to limit the number of results on a fetch request, but I'm not doing any fetches.

Because new entries come in every second or so, I need to either limit the number of entries displayed or make sure the table doesn't jump back to the selected entry when a new entry is inserted. I'm at a loss as to how to do either. Any ideas?

-- bradbeattie@gmail.com

----

Very hard to answer given the information available, but assuming that the result of the predicate filtering operation is an array, why not just copy the first 10 results? Since you're only moving arrays of pointers around, it won't be too inefficient. If the array is being displayed in a table, your data provider methods could also simply return 10 or less as the number of items, and then only the first 10 will be displayed. That said, I have not yet played with Core Data to any degree so it may be obscuring some of these details and methods from you. --GrahamCox

(update: having had a quick scan over the docs regarding NSArrayController and NSPredicate, it seems that this is indeed the case. Hmmm... not sure... --GC)

----

Okay, assuming there's no easy way of limiting the number of entries in the table, is there a way to ensure that the table doesn't scroll back automatically to the selected entry whenever there's a new row inserted? (That happens almost every second).

----

This seems to be very similar to implementing live search � that is, just create an NSArrayController subclass with an implementation of -arrangeObjects that will only return the top 10 results.

