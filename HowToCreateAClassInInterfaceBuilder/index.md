---
layout: page
title: HowToCreateAClassInInterfaceBuilder
---



In Interface Builder, go to the "MainMenu.nib" window and click on the "Classes" pane.  All classes you create are subclasses of already existing classes.  In this example, I show you how to create a subclass of NSObject, but you can use the same procedure to create subclasses of other classes.  Here it goes.  Highlight the word "NSObject" and hit return.  This creates a subclass of the class "NSObject". Now type in the name of the class you want to create.  That is it.

You can also add classes you have created on your own. Simply drag the icons for any header files you wish to import into the document window in IB. If you have created your header files correctly, IB will read the class's actions and outlets, as well as the superclass, and automatically add your class to the nib file.

Go back to HowToProgramInOSX

