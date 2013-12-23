---
layout: page
title: NSToolbarImagePopup
---

Hi all!

I'm trying to implement a special item in my toolbar.

I've downloaded the code for an NSPupUpButton subclass from http://www.iratescotsman.com and tried to use it in a toolbar.

The issue I have is that although I can get the custom button to appear in the Toolbar itself, it will not appear in the Customize Toolbar panel. I've tried yelling at it, tickling it, throwing stale slices of bread at it but to no avail. Then I ran out of stale bread...

Ok, enough prebaked humor, here is a link to my source code for a sample app: http://homepage.mac.com/renaud/FileSharing5.html --> it's in the Cocoa section.

Oh, and I added NSCoding support to the original code as it seems NSToolbar does stuff with NSCoding.
Then I added code to generate a new toolbar item (the button itself actually) every time it was called in toolbar:itemForItemIdentifier:itemIdent willBeInsertedIntoToolbar: is called... but again, no work.

Anyone care to figure it out or provide more silly ideas I can try?

Thx

- Renaud

*Have you tried getting a normal NSPopUpButton working then just change the custom class in IB?*

Yup, doesn't work... once I change the class in IB, the project won't launch, stating that the image cannot be cached... is it possible the actual subclass is lacking somehow? I've mailed the author and he should reply this weekend if he has time...

Final solution: In my routine for initWithCoder for the NSButtonCell, I had to add:
	_buttonCell = [[NSButtonCell alloc] initTextCell: @""];	
	[_buttonCell setBordered: NO];
	[self setArrowImage: [NSImage imageNamed: @"ArrowPointingDown"]];

And now everything works! I'll post the fixed code at http://homepage.mac.com/renaud/FileSharing5.html if you want it.

----

I downloaded Renaud's code to test it out. He has not put any documention into the readme nor into the code itself, so here is a quick synopsis. He has added an item that displays a large color square and a tiny down arrow to the right. If you press the box, it works like a button. If you carefully click on the arrow, you get a popup menu (that lets you choose the color of the square). Code is formatted well, but no comments. Its all based on Apple's Simple Toolbars sample.

David

PS: I revisited the Irate Scotsman link, and found an updated ImagePopup enhanced by Eric Wang: http://iratescotsman.com/ftp/source/imagepopupbutton.tgz
This code compiles with no errors on 10.4.1 and looks nice. You get some documentation when you run the App.

PSS: I would still like to get a control like the Safari left-right segmented control. It moves you left, right, or if you hold the mouse down for a while you get a popup.

----

Just a note about that last link. I haven't touched the code in a long while, I have no idea if it works with Tiger or not. Don't look at me if your computer blows up using it ;-) Maybe one of these days, I will get around to updating it and writing some documentation.

-EricWang

