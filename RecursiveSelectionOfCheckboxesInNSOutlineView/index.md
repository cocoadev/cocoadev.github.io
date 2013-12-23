---
layout: page
title: RecursiveSelectionOfCheckboxesInNSOutlineView
---

Hello I'm kinda stuck on a problem I've been working on.  This is my first entry on CocoaDev so bear with me :)
I created a NSOutlineview with two columns.  One has an NSSwitchButton (checkbox) data cell, the other one a text and image data cell.
Everything so far works fine. 
I populated my NSOutlineView with 3 root items, each a directory containing files.
My question is this.  I would like that when I press on a root item checkbox all its children's checkboxes get selected if they're not selected.
Also that when I uncheck a child I would like that the root item checkbox goes to mixed state to signal an incomplete selection of its children. (OS X installer style)
Can anyone point me into the right direction?  Which delegate/data source method should I use to accomplish that. (or is it none of those?)
thanks
MOMO


----

I had to do something similar to this recently, and was kind of surprised that I couldn't find any delegate methods for NSButton to do what I wanted.
I felt like I was cheating, but I ended up binding an IBAction to all of the checkboxes, and every time one was checked, it calculated what the state of the root checkbox should be by hand.

clunky, but it works...  
dhaveconfig
 

----

You need to use the method �  outlineView:setObjectValue:forTableColumn:byItem:  to catch the value being changed.  To enforce the rules you want you will basicallu just have to implement the appropriate logic in that method.  You manage the source data so just do the appropriate thing in the model then call reloadData to have the outlineView refresh itself.  Presumably you are providing the values for the checkbox column via the dataSource method so if you make sure the model looks how you want it to after the set method is called, the view should reflect that when it is reloaded.

JKP

----

Thanks JKP that worked :) I loaded the children of the item provided by outlineView:setObjectValue:forTableColumn:byItem: and changed their button state ,then reloaded the item and children.  works like a charm :)
thanks

Mo

