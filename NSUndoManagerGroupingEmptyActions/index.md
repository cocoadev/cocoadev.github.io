---
layout: page
title: NSUndoManagerGroupingEmptyActions
---

Implementing undo of a mouse operation, I start an undo group on mouseDown, and end it on mouseUp. If the mouse actually moves and does something, then all is well, but if it doesn't, I get an empty group with no undo actions in it. This still shows up as an "Undo" item in the menu, which does nothing. It would be great if NSUndoManager detected that a group was empty and simply ignore it, but it doesn't -- so my question is, how should I implement grouping to avoid this problem? --GrahamCox

----

Why not just start the group as soon as the first mouse movement actually does something?

----

Exactly. Setter methods that actually modifies your data is the best place to handle undo-stuff. Just stick with this pattern and I bet it will save your a lot of time if you decide to create several ways to edit one piece of data or something more advanced. -- DenisGryzlov

----

For the most part I do use setter methods, but consider the case of a setter method such as moveToPoint:(NSPoint) location, on a given object, where this method is called as part of a mouse dragging loop OR by something that simply sets the location. In the loop case, as many undo tasks are recorded as mouse events. When you choose Undo, you only get the last event, so undoing the action can take hundreds of Undos. By grouping them, all of these individual events are grouped into one Undo, so the Undo does the expected thing. Now to my mind this isn't very efficient - it might be better to record the state prior to any movement, then submit that previous state to the undo manager on a mouse up, but this breaks the generality of the moveToPoint: method. By leaving the setter method to handle undo, the necessity of grouping it means that you still need to do undo group related stuff at mouse down/dragged/up time. Whichever way you do it, it's a bit messy when undoing mouse drags, and it seems as if the undo manager hasn't really been designed with this in mind... or else I've missed some totally different and better way to do this. By the way I solved the problem I was having in the way suggested - by waiting until a mouseDragged to set the grouping, and setting a flag so that mouseUp knows to end the grouping. Works like a charm, so thanks for that. The efficiency question remains however. --GrahamCox.

