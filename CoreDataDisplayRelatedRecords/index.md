---
layout: page
title: CoreDataDisplayRelatedRecords
---



I am having a frustrating time trying to set up an NSComboBox or NSPopUpButton to do something I think should be quite simple.

I have an NSManagedContext with two entities A & B, with a one to many relationship from A to B.

I have set up a window with two NSTableViews with two corresponding NSArrayControllers, one for each entity. The content set for the controller for B is bound to the controller for A, with the 'selection' controller key and the model key path being the one to many relationship between A & B.

This achieves the desired behaviour of the NSTableView for B, only displaying records related to the selection in the NSTableView for A.

So far so good. As an improvement, I would like to make the selection of A in a NSComboBox or NSPopUpButton rather than selecting a line in its NSTableView.

However, I have failed to find the correct settings for either of these controls, though I have tried pretty much all combinations.

Can someone please enlighten me?

-----------------------------

Hi

The NSPersistent Document Tutorial in XCode documentation (Cocoa/Core Data) explains how to do it with a NSPopUpButton. You must not bound the content set for controller B to controller A. You should instead set for the NSPopUpButton :

    � content to arrangedObject of controler B

    � contentValues to the attribute of B you want to display in the NSPopUpButton

    � selectedObject to selection of controler A


Good luck.

