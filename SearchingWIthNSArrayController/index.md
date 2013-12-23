---
layout: page
title: SearchingWIthNSArrayController
---



I have a problem with NSArrayController. Specifically I have (1) a Controller class (2) an NSObjectController (3) a NSArrayController which ties to an array in the controller class through the object contoller, this array controller is also responsible for displaying the contents of a NSTableView because each table column is bound to the array controller. I could easily create, update and remove records in this table view using Cocoa bindings which worked excellently.

I however also wanted to add iTunes-like searching to this table view which I've never done before. However Apple had a page about this in their documentation and it was relatively easy to follow. It said to create your own custom NSArrayController class and implement 2 simple methods that would take a search term from a NSSearchField and display only the relavant records just like iTunes and to use this class to override the default class of the array controller  through the custom class pane.

Everything was working fine up until this point when I discovered that as soon as I enabled this Search function, the table lost the ability to sort records. Sorting of records was enabled through bindings and worked great, but somehow the instant I overrode the default NSArrayController class with my own, it took away some of the functionality the existing class was providing. Is there something I need to do to restore simple things that bindings had done on its own because I've overridden the default array controller class with my own? Otherwise right now I have to choose between searching and sorting records.

I tried creating a 2nd array controller class in the same NIB, that pointed to the same object, but that failed to yield any results. And I've also tried isolating the search field in another NIB and reimplementing it, but that's failed to yield results as well. I get this feeling that the custom array controller class should automatically handle this stuff all the same since it's inheriting from NSArrayController. I'm stumped right now.

----

There are many bindings examples at the following website, including one that deals with filtering an NSArrayController based on a search. Maybe you can solve your problem through these examples: http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

----

The filtering code (which I haven't seen) probably hooks into the NSArrayController by overriding     -arrangeObjects:, which normally handles sorting.  As such, you have to be careful to call     [super arrangeObjects:myFilteredArray], after you've filtered the array and return the result of that call to make sure it's both filtered and sorted.  We'll probably need to see the code for your NSArrayController subclass to help further.  -- Bo

----

Polling the general opinion about disabling the capacity of a table view to add new rows while a search field is active:

The table is implemented with CocoaBindings and has an array controller.

The reason why I want to do this is because of complications induced by adding objects to the array controller's arrangedObjects array
while the array controller is filtering the original array into a filtered array. You have to make a special provision to add an object
while filtering is ongoing, and in a typical implementation, the added object must be displayed whether or not it meets the search criteria.

I think this is ugly, even if not exactly evil, and IMO, the best way around it is to disable adding objects while the search is active.

Other opinions?

----

I do this in my app, and no users seem to mind :-).

- Davey

----

Disabling sounds like the Right Thing to me. If the add functionality isn't disabled, and the user adds an object that doesn't meet the search criteria, the possibilities aren't very promising. Either the object gets added invisibly, or the user's search results are useless. I suppose you could pop up some kind of notification ('blah was added, but will not be shown under your current search criteria') but that sounds like a lot of work for very little benefit.

