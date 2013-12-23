---
layout: page
title: IBPalette
---



http://cocoadevcentral.com/articles/000046.php

One thing that caught me off guard that I thought I would share. If a palette that you create is able to run fine in IB, but raises the exception "NSInvalidUnarchiveOperationException" when you try to run an application that uses a control from the palette, you probably forgot to add the custom view to the target membership of the application (e.g. if the custom view is the class DotView, you have to add "DotView.h" and "DotView.m" to the target). IB loads the object code for DotView from the palette, but your application will load the object code for DotView from it's own bundle.

If you create a custom class and you want to put it in an IBPalette, you have to implement NSCoding.

see also HowDoIBPalettesWork

Palette projects on CocoaDev:

*GenericToolbar by JediKnil: NSToolbar in Interface Builder
*FoundationCollectionsPalette by KritTer: A palette that allows you to add N<nowiki/>SArrays and N<nowiki/>SDictionaries to your nib file.
*RBSplitView
**anyone else?*


See also http://docs.sun.com/db/doc/802-2110/6i63kq4uj?a=view -- Sun's OpenStep documentation for IBPalette (and the rest of the InterfaceBuilder.framework). It's fairly out-of-date, but better than most of what Apple has. You'll have to compare with the current framework headers to make your palette work.

----

If anyone wants help with IBPalette-related problems, just ask me. I've run into (and still am hitting) most of them making GenericToolbar. --JediKnil

----

Does anyone know what Tools > Palettes > New Palette (in the Interface Builder menu bar) is supposed to do? It creates a new palette (named "untitled"), but what can you do with it after that?

*According to Apple's docs:*

*� What is a dynamic palette and how do I make one?*

*Dynamic palettes are custom palettes created by the user. Select the menu item Tools > Palettes > New Palette. A blank palette is created for you. You can now Option-drag widgets from the design window onto the palette. Option-drags containing multiple widgets come off the palette as one unit. Save the palette using the menu item Tools > Palettes > Save.*

----

I am looking for a NIB palette that shows a one month calendar view (Like iCal)

----

try ObjectLibrary

----

Or even better, in Tiger at least, use a NSDatePicker control and set the style to "Graphical". This gives you exactly the same control as in iCal or System Prefs.

----
Where are these put when they're installed?

----
If I'm not mistaken, they stay where they were at when you add them, and IB simply links to it from there.

