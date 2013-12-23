---
layout: page
title: NSPredicate
---

Class documentation:
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSPredicate_Class/index.html

Predicate programming guide:
http://developer.apple.com/documentation/Cocoa/Conceptual/Predicates/index.html

From the documentation:
The NSPredicate class is used to define logical conditions used to constrain a search either for a fetch or for in-memory filtering.


NSPredicate is used if you, for example, use CocoaBindings and want to have a search-field filtering the contents of a NSTable. Lets say you have an array of Person-objects with name, address etc. controller by the NSArrayController PersonController you just add a NSSearchField and bind its predicate-bindings to PersonController, controller key filterPredicate and enters something like "name contains[cd] $value" in the predicate format text field. "name" represents the keypath of the searched object, "contains[cd]" is the comparison operation which in this case ignores case [c] and ignores differences in accents [d] (for diacritic insensitive) and "$value" is the text entered by the user of your program.

By binding predicate a predicate2-binding is added to the search field. This makes it possible for the user to do multiple kinds of searches. Put a name in the Display name-text field to indicate what kind of search is made. This makes it possible to have a "search by name", "search by address", "search all" alternatives in the same search field. The user chooses what he/she wants to do by clicking the magnifying glass which will display a menu with the options.

