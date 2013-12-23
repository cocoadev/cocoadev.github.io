---
layout: page
title: CategoriesAreBad
---

I believe there are a few distinct areas where CategoriesAreGood, and there are other areas where CategoriesAreBad. Under what circumstances are categories bad?

-- MikeTrent


* When they pollute the namespace of a class and all its descendants: 
* causing perplexing errors if a descendant accidentally overrides the new method
* causing naming headaches because one cannot have two categories on a class with the same method name in both
* causing headaches when something checking -respondsToSelector: can no longer be sure if the method's what it should be

* When they cause a ProtocolsTooCasualSmell
* When they silence compiler warnings that they really shouldn't
* When the functionality they add should really be in a different class
* Maintenance issues when dealing with someone else's code, such as they give you code that uses one of their categories, but don't remember to give you the category.  Also, someone uses a category thinking it's a built-in Cocoa feature, and then complaining that Cocoa is broken, when it is actually someone else's category.
* (insert here)


See also ClassCategories for general discussion on categories and what you *can* use them for; after that, it's up to you to decide if CategoriesAreGood or CategoriesAreBad for your situation.

