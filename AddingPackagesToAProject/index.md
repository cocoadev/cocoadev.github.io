---
layout: page
title: AddingPackagesToAProject
---

I want to add a bundle to my project, just so that after compilation its located in a certain folder in my application bundle.  But every time I add the bundle to my xcode project, it just adds the files inside the bundle and creates the directory structure around it in the xCode directory listing, which is very annoying, making it so I have to go in an manually do show package contents> and add the bundle myself afterwards.  Does anyone know how to appropriately do this?

----

When you add the bundle, Xcode will bring up a sheet with some options. Check the option that says something like, "Create a folder reference". This way Xcode will create an actual reference to a folder, instead of a group that contains the folder's contents.

----

If you want it to end up in the application bundle, try adding a "Copy Files" build phase.  It allows copying of things like private frameworks into the application bundle sub-directories.

