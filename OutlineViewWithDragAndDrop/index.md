---
layout: page
title: OutlineViewWithDragAndDrop
---



I'm beginner to Cocoa Application by using ObjectiveC.

Recently I am studying how to use NSOutlineView.From reading  the discussions I have created an NSOutlineView and implemented those required methods and it can be edited. But I can't implement the drag and drop (exactly, the drop) however I try to work hard.

In awakeFromNib I have registered the dragged types and written the following two methods,

- (BOOL)outlineView:(NSOutlineView *)olv writeItems:(NSArray *)items toPasteboard:(NSPasteboard *)pboard

- (unsigned int)outlineView:(NSOutlineView*)olv validateDrop:(id<NSDraggingInfo>)info proposedItem:(id)item proposedChildIndex:(int)childIndex

but I don't know how to write the acceptDrop method and why first the value of childIndex is -1 when the application is run.

- (BOOL)outlineView:(NSOutlineView*)olv acceptDrop:(id <NSDraggingInfo>)info item:(id)targetItem childIndex:(int)childIndex

And I try to read the sample file:///Developer/Examples/AppKit/DragNDropOutlineView/. But I can't understand it.It's too hard for me.

I really appreciate if someone can upload a simple sample about the drag and drop of NSOutlineView or give me some advice.

Thanks for any help.

Best regards 

lepton

----

There are quite a number of existing pages at this wiki that purport to deal with your question. I hope you find them helpful. CocoaDev also has a very useful search capability which is located at the FindPage page. You can do a local search by keyword or with Google. Many other pages (50 or more) at CocoaDev besides those listed below also discuss using NSOutlineView.


*NSOutlineViewDragAndDrop
*NSOutlineViewDragAndDropTutorial
*NSOutlineViewDraggingRows
*DeletingInNSOutlineView
*NSOutlineViewChildSelectionHelp


What most of these pages may demonstrate is that developing the capability is not utterly trivial; others have had this sort of difficulty before. Perhaps it is also true that those who have developed a solution are not eager to share it. Keep working with the Apple example and eventually it will make sense to you. I hope someone will come forward with an insight about how to set up your child index.

