---
layout: page
title: HowToReadClassFilesFromProjectBuilderIntoInterfaceBuilder
---

To get class files from Project Builder into Interface Builder, you must first go into Interface Builder.    Next, go to the "Classes" tab of the "MainMenu.nib" window and select the class whose information you want to get from Project Builder.  If you have not yet create the class in Interface Builder do that by selecting the superclass (such as NSObject or NSView) and hit the return button.  Then type in the name of your class.  Once the class is selected in the "Classes" tab, go to the menu "Classes" and choose "Read MyClass.h" where "MyClass" is the name of your class.  That should do it.

Back to HowToProgramInOSX

