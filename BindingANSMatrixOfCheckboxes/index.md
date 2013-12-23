---
layout: page
title: BindingANSMatrixOfCheckboxes
---


My script is working good if i use radiobuttons :
It's a very simple thing : a controller with an array, a NSObjectController in my nib with a NSArrayController binded to it.
Then the NSMatrix is set to mode radio, prototype to NSButtonCell of type radio. Finally, the NSMatrix SelectedIndex is binded to the NSArrayController's and everything is working good.

Now : 
I want check boxes to have multiple selection. I changed the mode of the NSMatrix to Highlight and the prototype type to Check Box.
But there is no way to Value Selection of type SelectedIndexes for the NSMatrix. How can i bind this NSMatrix of Check Boxes then?

UPDATE: does really nobody tried that before? Maybe i should use a NSTableView?

