---
layout: page
title: OutletVariable
---



An outlet variable is a specialized InstanceVariable of AnObject which is connected to an instance of a ResponderObject. In other words, an outlet is a variable whose value is an object in the UserInterface, such as, a window, a control or a view.  

**CAUTION:** When adding OutletVariable(s) to a class in InterfaceBuilder for an existing InterfaceFile, do not select 'Create Files' as InterfaceBuilder does not yet merge its output with existing files, in other words InterfaceBuilder will overwrite your existing files. You will need to add the new outlets by hand to the interface file.

    @interface MyControllerObject : NSObject
{
	IBOutlet id	myOutlet; // InterfaceBuilder generated outlets look like this 
	IBOutlet NSWindow *myWindowOutlet;
	IBOutlet NSControl *myControlOutlet;
}
// ClassMethod declarations and InstanceMethod declarations
@end

----

A simpler way of putting it, maybe: 

An outlet is simply a member variable of a class.  The only special thing about outlets that makes them different from any other instance variable, is you can connect another object to your outlet using InterfaceBuilder.

Why is this useful?  Say my main controller object needs to know about a button and a text field in my main window, it is very easy to arrange that.  I would simply add two outlets to the main controller, and connect them to the button and the text field respectively using InterfaceBuilder.

Now, as soon as my controller object is "unfrozen" from the NibFile, it will have valid pointers to the button and the text field, ready for use.

To make a comparison to PowerPlant, normally you would have to override FinishCreateSelf() for your window subclass, and call FindPaneByID() on each of the widgets you were interested in talking to, store the pointers somewhere, and deal with the possibility that you might bump something and get a NULL pointer back.

Outlets eliminate the confusion -- the RunTime finds the widgets you have made connections too, and assigns them to your outlets, as per your InterfaceBuilder connections.

Very simple concept, but extremely powerful.

-- StevenFrank

----

Let's say I have an object that doesn't have an explicit action - I just need to know about its state, for example a radio button or checkbox. How would I connect that in Interface Builder?

*Ideally, you use the ModelViewController paradigm and don't store any kind of state information with the button or checkbox. Store it in a separate model object, get the view's action to inform the model there's a change needed (possibly via a controller), and get the information about state from the model. -- KritTer*

**I think what you need to do is connect it as an outlet.  Add an outlet from your class, and connect the checkbox to the outlet.  Then you can check up on the status of the checkbox with its commands.  Hope I helped... I'm a newbie too.**

