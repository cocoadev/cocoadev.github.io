---
layout: page
title: HowToUseOutlets
---

Outlets are used to place information into interface objects or into another class. For instance, you might want to change the name of a button or an NSTextField programmatically.  To do this you would create an outlet for this button or NSTextField in your class.  Or you might want to access the variables or methods from another class.  All this is possible through the magic of outlets.

** Prerequisite to creating an outlet **

But first, you must have created a class.  You can either create your class in Project Builder or in Interface Builder...

* HowToCreateAClassInInterfaceBuilder, or
*  HowToCreateAClassInProjectBuilder.


Now that you have created your class, to work with outlets you can follow one of these two procedures.  

** Procedure One - Start in Project Builder **

* Create the outlet (see HowToCodeAnOutletInProjectBuilder),
* Read the outlet into Interface Builder (see HowToReadClassFilesFromProjectBuilderIntoInterfaceBuilder).
* Connect the outlet in Interface Builder (see HowToConnectAnOutlet).


** Procedure Two - Start in Interface Builder **

* Create the outlet (see HowToCreateAnOutletInInterfaceBuilder
* Connect the outlet (see HowToConnectAnOutlet), and
* Save the class into Project Builder (see HowToSaveAClassCreatedInInterfaceBuilderToProjectBuilder).


The methodology for creating an outlet is similiar to creating an action method.   In Interface Builder, you control drag from the object sending the information to the object receiving the information.

For example, to send information to an interface widget from your class, do the following.   First, go into Interface Builder and create your interface objects.  After this, select the class in which you want to put your outlets, and add the required number of outlets using the info palette. Then, control drag (similar to actions) from the class to the interface object.  Select the outlet and click the button "Connect" in the info palette. 

Then, in your code you can call the outlet.  For instance, if you created an outlet called MyTextField, you can add text to it by using the following code
     [MyTextField setStringValue:@"Hello World!"];
This code will place the words "Hello World" in your NSTextField that you have linked to with the name MyTextField.

If you want to be able to get and send information from one class into another, you can use outlets.  In Interface Builder, create an outlet in the class you want to use to get or send information.  When you create this outlet, give it the same name as the OTHER class.  Then control-drag from this first class to OTHER class.  Select the outlet, which is the name of the OTHER class, and click the button "Connect" in the info palette.

Once the outlets have been created and connected, now you need to save them into Project Builder.  To do this see HowToSaveAClassCreatedInInterfaceBuilderToProjectBuilder

Back to HowToProgramInOSX

