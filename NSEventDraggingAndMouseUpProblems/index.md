---
layout: page
title: NSEventDraggingAndMouseUpProblems
---



I'm having some trouble getting dragging to work, it *did* work before, now it doesn't and I cannot see the problem, here is the problem, when I mouse down i get the regular event, however even though the view is first responder it only sends one mouseDragged event to my view on mouse up and no mouseUp event, here is the code:

Self found solution:

remove     [super mouseDown:event];

----

Mmm if only you could write code to fix your own code :)    [self findSolution];

