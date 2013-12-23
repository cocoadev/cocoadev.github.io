---
layout: page
title: InterfaceBuilderPreviewProblem
---

Using x-Code 2.1

I am designing a nestled interface, being  one large tabbed view (Not showing tabs)
In this I have a split view where this consists of one box and another tabbed view (also no tabs showing)

This is so that I can swap part of the main tab view and also swap the entrire view.

I use command + R to preview the GUI elements so that I get the allignment and drag properties right.

Suddenly when I have the main tab open and the second tab in the nestled view open the command + R no longer works.

I can see the menu item being selected but no result of this.

Can any one shed some light ?
----
Well, one possible problem could relate to usage of custom classes. Try opening the Console application (/Applications/Utilities/Console) before testing the interface, and see if you get any error messages from Interface Builder. --JediKnil

