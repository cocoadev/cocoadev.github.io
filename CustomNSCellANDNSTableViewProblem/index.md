---
layout: page
title: CustomNSCellANDNSTableViewProblem
---


Hello, I have created a custom subclass of NSActionCell, which has one image and two NSTextField labels like -> 'I:' where the I is the image and the two dots in ':' are the textfields. However, when i place these in a NSTableView, the rows overlap! That is the 2nd textfield of the first row appears and the subsequent rows overlap with their respective subsequent rows.
When i place the corresponding control in a NSView, it appears okay.
I have overridden the cellSize and cellSizeforBounds methods, to no avail (they are not called).

P.S I'm not sure if I should have placed this here or in NSTableView / NSCell - if placing this there is a better option, i"ll go ahead and do that.,
Thanks
SiSG

