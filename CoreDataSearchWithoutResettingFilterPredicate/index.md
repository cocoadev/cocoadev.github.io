---
layout: page
title: CoreDataSearchWithoutResettingFilterPredicate
---



Hello everybody,

Maybe theres an example of this somewhere, but so far I am unable to find it. As you know when you prototype an UI with Core Data entities in Interface Builder it creates (amongst other things) a NSSearchField that searches the entities using a filter predicate. In my app there is a a source list containing a static item "all items" and then an item for each category for which all entities in the table belong to. When you click on a category in the source list it sets the filter predicate to only show all the entities in the table that have their category set to the selected category in the source list.

When the "All items" is selected it sends setFilterPredicate:nil]; to the array controller for the table, so the NSSearchField is able to work there, but as soon as you click on a category it renders the NSSearchField unusable. Is there a way to filter the entities im looking for and search within that array?

