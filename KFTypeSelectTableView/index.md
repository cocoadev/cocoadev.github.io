---
layout: page
title: KFTypeSelectTableView
---

The discussion on TypeAhead last week inspired me to hurry up and release my NSTableView subclass.

KFTypeSelectTableView adds the type-select, or type-ahead, feature to table views. When a table has keyboard focus,

* Typing a few characters selects a matching table row.
* ctrl-down-arrow and ctrl-up-arrow and select the next and previous match.
* ESC and cmd-. cancel.


As a user, I'm tired of apps not supporting this feature, so I'm trying to write a class that there is no reason not to use. :-)  It doesn't require any configuration unless you're using bindings, and it's BSD licensed.  Even when you do want to configure behavior, almost all logic stays in the subview class.  You can pose with it and get type-select in all tables (and outline views too!).

You can get it from http://homepage.mac.com/kenferry/software.html#KFTypeSelectTableView.

The project source also has a couple of interesting applications in it. There's a plugin for Mail.app, and a SIMBL module that adds type-select to every tableview system wide.

I'd like to hear it if anyone here has any input.  That can be of any sort, including suggestions of how to rework the API, or capability you'd like that isn't in the class. 

----

Many thanks for this useful widget!

----

Hmm... I wish I'd seen this.  Just implemented the same feature independently into AIOutlineView for NSOutlineViews.  It's available via svn at http://svn.adiumx.com/svn/adium/trunk/Frameworks/AIUtilities%20Framework/Source/AIOutlineView.m (AIUtilities doesn't have its own site yet).  We should compare notes.

-evands

----

Yes, definitely.  I'm looking at the Adium source now. -KenFerry

----

Evan and I discussed and merged efforts.  KFTypeSelectTableView picked up a search wrapping API from Adium, and Adium now uses KFTypeSelectTableView.

-KenFerry

