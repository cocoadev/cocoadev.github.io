---
layout: page
title: ButtonIcon
---

My question is simple: How to add an icon to a button using Interface builder?
I didn't found any valuale information about this in books related to Cocoa programming and developing aplications for Mac OS.
I know that I can simply drag and drop icon file to a button, but I don't know in which format should be button icon:
.pict, .icns, .rsrc or in any other image format?
I tried mentioned files by dragging from Finder window to an image button in Interface builder but it is not worked.
I dont' need an Alternate icon, and don't need to add image programmatically, just simply adding icon to button using interface builder.

----

The image needs to be in your Xcode project first. Drag the image (for user interface, use tiffs or pngs) into your Resources group in Xcode. Then switch to Interface Builder, and go to the attributes for your button and enter the name of the image you just dragged into Xcode into the Icon field. If the filename of the image is "MyIcon.tif", then you enter MyIcon for the name.

----

Adding: There's a bit of a "gotcha" here, though. Sometimes (every time) if you already have your nib open in Interface Builder when you add a new image to your project's resources, a "broken image" icon appears when you set the image in IB. To fix this, close the nib and re-open it. The image should display properly.

