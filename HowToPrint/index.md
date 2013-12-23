---
layout: page
title: HowToPrint
---

In order to print, you need to have a space in your window that will print.  This can be a CustomView region that you link to a subclass of NSView.  When I did it, all I needed to do was hook up the Print item in the File menu using ctrl-drag to the myView object in Interface Builder.  Then I disconnected the previous connection and connected it to the print: action of the myView object.  Works like a charm.

To print in a document based project, things are much more tricky.  Anyone care to explain below?


Go back to HowToProgramInOSX

