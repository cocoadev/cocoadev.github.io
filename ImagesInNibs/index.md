---
layout: page
title: ImagesInNibs
---

Has anyone else had problems using images in NIBs in the version of Interface Builder that ships with Xcode?

I am trying to use images as icons for buttons and for some reason they aren't working. I have even tried using images from Apple's own apps, and no dice.

----
I've sucessfully used images as icons for buttons with Xcode's IB.  I have found for some projects created in Project Builder,  Xcode sometimes don't put resource in the right place inside the application bundle.  Open up your application bundle and make sure that all your images are inside the Resources folder.

-HuaYingLing

----

This is a project created with Xcode, and the images aren't even showing up properly in IB.

And the run output says that it cannot read Apple's own TIFF images.

----

Putting images in nib files is deprecated as of 10.1 ( i think ). You should be referencing external image files using the inspector panel in IB.

----

I'm not putting the images in the NIB bundle, I am setting them via the inspector, and I have even done it programmatically.

----

Do the images have .tif or .tiff extensions? I know this could be a problem in the past. Try the other extension if these are in fact TIFF images.

