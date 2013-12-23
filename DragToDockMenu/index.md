---
layout: page
title: DragToDockMenu
---


You can drag to a menu; you can drag to the dock - but can you drag to a Dock Menu?  eg to open a document different ways based on which item in a dock menu you drag to.

----

I don't believe so, no.

*Since when can you drag to a menu?*

----

Look at this:

http://www.ambrosiasw.com/utilities/dragster/

It uses Accessibility API to locate the position of its icon on the Dock, and then draw a floating window which looks like dock menu. By the way, how can it use  accessibility API when I disabled "access for assistive devices" in System Preferences? 

----

Use AXMakeProcessTrusted or
    
sudo chown :accessibility myApp
sudo chmod g+s myApp

in Tiger.

----

If you must, then authenticate and use a small helper program to do the dirty stuff, but don't make the gui run as superuser..

