---
layout: page
title: UsingCriteriaViews
---

Can anyone give me a start, or a full tutorial if there's one out there, on how to create the criteria-style views that you see in Mail, Finder, iTunes, etc. I don't need the filtering part specifically, just the way to create the repeatable criteria views, you know, click add and it adds another row, click remove and it removes it. Also, I'd like to have it so that I can change the type with an General/NSPopUpButton like Mail and Finder. If someone can give me a push in the right direction I'd appreciate it. It seems to be a common question here but without a good solid answer. --General/LoganCollins

This is not really a treatment of the problem at hand, but an article on stepwise detailing how to use subviews in tableviews, uses a rule editor (criteria view, etc) to demonstrate. The source code is there.
http://www.stepwise.com/Articles/Technical/2003-12-20.01.html

----

*A similar topic (but from the Core Data standpoint) has been started here: General/RulesEditorWithCoreData*

----

When I first approached this problem I thought an General/NSTableView was the way to go, but eventually I realized that using a custom view was a lot easier than trying to make a tableview do something it's not really designed for. Once I figured this out, the solution was easy to implement and worked perfectly. To get it working, you'll need three classes:

*A main controller class for the rule editor window. Can be an General/NSWindowController, or whatever fits the project. Handles the user actions for adding and removing rules.
*A container General/NSView subclass. This is the superview for each rule view, and has a method for arranging its subviews so they're correctly spaced out. It will also draw the background, since you probably don't want your window to show through. In IB, you'll instantiate one of these and put it inside an General/NSScrollView.
*A rule controller. This is your controller for a single rule. Each time you create one, it will load a new copy of a rule nib, which contains an General/NSView with all your widgets in it. This General/NSView is what you put in the container view. All your rule controllers are kept in an array in the main controller.

So, that pretty much covers the basics. Depending on how familiar you are with Cocoa it may seem a little complex at first, but I guarantee once you start working it will become very clear. If you have any questions or want to see a little bit of code, just ask and I'll go into a little more detail. -DF

----

Code would help a lot. I haven't really dove into anything like this in Cocoa yet. Hopefully I won't be too dense to understand it. :) --General/LoganCollins

----

What about http://www.joar.com/code ?  JKP

*Nice demo. I'll be checking that out myself. ;-)*

That's the same demo that was posted above from stepwise.

*Hmmm ... I thought the former only showed how to use custom views in place of cells in a table, not a rules editor. It's been quite a long time since I've looked at it, though.*

----

I did a custom view for this pupose: General/PSRuleEditor. It's a framework so you can embed it in any TIGER application.
http://ruleeditor.googlecode.com/svn/wiki/General/RuleEditor.jpg

It does not use a table view so you can add different controls at each row (and not only popups or text fields).
It works with a delegate so it's quite flexible. 
Also supports predicates. You can bind a General/NSArrayController' s filterPredicate to it and filter a table.

The goal was to mimic the finder-spotlight Criteria view, so nothing magic : it's strongly inspired by Apple UI and design.
It is hosted under BSD licence at googlecode: http://code.google.com/p/ruleeditor


----

I have a small set of classes that implements this, for the exact purpose you mention - filtered lists of things based on a set of criteria rules you build up. I designed it to be totally general purpose and easily adapted to any typical situation. In fact I intended it as an article for General/MacTech, but somehow I managed to lose the entire write up (but not the code) and sort of gave it up. However, it works pretty well, is easy to use - I've dropped it into three or four apps now and it required very little effort. I'm more than happy to give away the code if anyone wants it; you can download it here: http://apptree.net/mpsearchquery.htm At present you'll have to rely on the comments for documentation, though it's pretty well commented compared to some. New docs to come when I can get to it.

The classes consist of: a) a pair of classes (General/MPSearchQuery/General/MPSearchCriterion) that actually performs a search based on a set of rules that you set up. This search is linear and relies on key-value coding to find the matches for the rules you set up. b) classes (General/MPAdvancedSearchController/General/MPSearchCriterionController) that implement a user interface for setting up part a. The UI relies on a delegate to supply it with a set of basic properties that can be searched for, and makes as few assumptions about the end use as possible, though it was very much based on iTunes' Smart Playlists concept. Hopefully it will be of use to somebody (apart from me!). --General/GrahamCox

P.S. My approach doesn't use a table view. It simply adds and removes views from the window as you add or subtract criteria. Each row is laid out using a template from a NIB file, so you can customise it how you want. Standard templates are there for most typical criteria - integers, strings, dates, values between two integer limits, etc. Actually the hard part was how to create copies of a view from a single template in this fashion, especially as General/NSView doesn't support the General/NSCopying protocol. Easy to get around as it does support General/NSCoding! --GC

----
A quick update on General/RuleEditor (http://code.google.com/p/ruleeditor) :
The last subversion rev supports selection, drag and drop and a simplified delegate allowing custom General/NSView for the right expression.
If you want, you can follow on-going development by subscribing to this google group : http://groups.google.com/group/psruleeditor-commits .
----
