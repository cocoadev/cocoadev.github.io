---
layout: page
title: UsingNSMatrixToEditTextFields
---

I'm having a problem with a General/NSMatrix of General/NSTextField. The matrix is part of a program which displays its data as a 12x11 matrix. The problem lies in displaying the data when it changes. I could make an outlet for every cell, but that'd mean 132 Outlets, and I imagine that can't be good.

My thought was to use the methods of General/NSMatrix to select a cell, and then change its value. But I'm not sure how or if this is possible (I'm very new to Cocoa). I tried something like General/matrix selectRow: column:] setIntValue] (the data I'm displaying are     int) but that crashes.

----

[[matrix cellAtRow: theRow column: theColumn] setIntValue: anInt];

- selectCellAtRow: column: (which is what I believe you meant by selectRow: column: because that doesn't exist) simply makes that cell the first responder (romakes it act like the first responder is probably more correct), and thus returns void, which means you were sending setIntValue: to void.

- [[FranciscoTolmasky

----

Is there a way to use the methods of General/NSMatrix to have the General/NSTextFieldCells display certain information, without making the 132 Outlets? I just need to pipe ints for them to display.

- Kenneth Hoffmann

----

    

General/NSMatrix *myMatrix= General/[[NSMatrix alloc] initWithFrame: someFrame mode: General/NSHighlightModeMatrix cellClass: General/NSTextFieldCell numberOfRows: 12 numberOfColumns: 11]];

[myMatrix setTarget: theDelegateOrWhatever];

[myMatrix setAction: @selector(someAction:)];

Then, in source of theDelegateOrWhatever, you would have this:

- (void)someAction:(id)sender

{

//sender is General/NSMatrix

General/NSTextFieldCell *theCellThatDidStuff= [sender selectedCell];

//do your thing.

}



someAction will be called whenever you do stuff to the nstextfield.  This should be what you want.  You can alternatively use initWithFrame:mode:prototype:numberOfRows:numberOfColumns: if you want to send a prototype of the textField you want instead of the class.  Also, if you just want to use interface builder to make your General/NSMatrix, then just do:

    
[myMatrix setTarget: theDelegateOrWhatever];

[myMatrix setAction: @selector(someAction:)];



in awakeFromNib and you can avoid initWithFrame:... 

- General/FranciscoTolmasky
