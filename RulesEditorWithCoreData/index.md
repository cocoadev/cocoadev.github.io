---
layout: page
title: RulesEditorWithCoreData
---



I'm interested in starting a discussion on creating a bare-bones, reusable rules editor GUI (and controller) for use with CoreData and its NSPredicate and NSCompoundPredicate functionality. For an example of a rules editor, see the Stepwise article titled Subviews In TableView Rows, found here: http://www.stepwise.com/Articles/Technical/2003-12-20.01.html ...

I admit I'm intimidated by the example above. To me it appears to be an angry, many-tooth'd thing. It's that reason alone that I'm not sure quite what it would take to make it reusable and nicely integrated with CoreData. My first impressions are that each 'row' in the table (represented by a view specifically-tailored to represent the rule) would represent an NSPredicate or NSCompoundPredicate. This could be stored in an array and could be bound to by other controllers' predicates binding. Finer details than that escape me at the moment.

Thoughts?

----

Here are mine:

Each row in a "Rules" table is not a predicate, but a custom Entity.  That makes most of the GUI fairly easy (bindings).  Then you just have a key called predicate or the like which returns a predicate compiled to the current rule.  Then whatever is consuming those rules can combine them with an NSCompoundPredicate.  (Or, you could have a binding adapter which observes the entities for changes, compiles the compound predicate, and notifies a client controller of the new predicate.)

Even better, have a second entity which has a to-many relationship to the rules entity.  This combines them with boolean logic.

Evil thoughts...   Maybe I'll try to throw something together (will spare the fancy NSTableView stuff for those who care)

*I'm not quite sure whether this is a better way. Wouldn't making each rule an entity be trampling all over the model/view/controller barriers?*

I guess it depends on what you're planning to do with this.  Mainly, is this just a filter for an NSArrayController, or are these rules defining application logic.  I guess the easiest way to tell is whether or not they should be persisted.  My train of thought was for something like Mail.app's rules, so I do see why this could be overkill. 

*I'm looking for the most extensible, reusable use of this. For my most immediate needs, I want to be able to define smart lists in my application.*

----


I care! BTW, thanks for the link to the custom views for table views. been looking for those for ages!

----

Okay, Mail.app appears to use a nib called "RuleCriterionView.nib" which contains (you guessed it) a universal 'rule view'. This part appears to be **fairly** easily reproduced (just time-consuming). I don't think the Subviews In TableView Rows approach is necessary; simply adding the criterion views as subviews in a scroll view should work just fine. Now for the back-end. I don't agree that a rule should be a custom entity. That seems over-complicated and it does not allow the resulting predicate (or compound predicate) to be used with non-Core Data applications.

----

There's a project on Google Code, ruleeditor, which aims to create a reusable rule editor for 10.4 and above:

http://code.google.com/p/ruleeditor/

You can pass in an NSPredicate (or NSCompoundPredicate) and have the rule editor laid out accordingly.

