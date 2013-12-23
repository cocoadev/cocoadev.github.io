---
layout: page
title: HowToGetInputFromTheUser
---

The user can give input in many ways, through buttons, menu items, typing text in a text box, etc.  To handle the user input stuff, I would suggest putting it all in one place in your code.  To do this, you can create a class such as "MyController".  In this class you can set up all the things you want the buttons to do.  For example, you can set it up so that when the user presses a button, then a particular action is taken by your program.  You give this action a name and you configure this action so that it does exactly what you want.

To get input from the user, make sure you are in Interface Builder.  Then...

  *create and instantiate the "MyController" class (needs to be a subclass of NSObject) - see HowToCreateAClassInInterfaceBuilder and HowToInstantiateAClassInInterfaceBuilder,
  *create a button - see HowToCreateAButton,
  *create a name for the action you want to take place and record this in the "MyController" class - see HowToCreateAnActionMethodUsingInterfaceBuilder,
  *link the name of the action to the button - see HowToLinkAButtonToAnActionMethod, and
  *write all the code for the action method in the implemenation (.m) file in XCode - see HowToMakeAnActionMethod.
  

Go back to HowToProgramInOSX

