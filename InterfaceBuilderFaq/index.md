---
layout: page
title: InterfaceBuilderFaq
---



A FAQ for InterfaceBuilder users. Please add more.

----
**How do I make a scrolling view in InterfaceBuilder?**

Select the view item you wish to place in a scrolling view. Select the 'Scroll View' menu item under the 'Make Subview Of' submenu of the 'Layout' menu. You now have a scrolling view.

----
**How do I send messages to interface elements in a programmatic fashion?(~or~ What's that stupid NSTextField called, anyway!?)**

This seemingly daunting task isn't so hard, really.  Here's an example:

The Goal:  A text field.  Type into it.  Hit a button.  The words show up in a System Text-type text field right below the button.

To do it(verbose):  
1: subclass NSTextField for your system-text-type text field at the bottom.  Add an outlet for the text field to be typed into, and an action that will show off the contents of that text field.

2: arrange your UI, do whatever you want. make it purty.

3: change the System-text-type text field's class to your custom text class. (click on the thing, hit cmd-6, change it to your class.)

4: connect your special text field to the editable text field, connecting it to your special outlet.  connect the button to your special text field as 'target', with the action you made earlier.

5: create the files for your text field class.

6: in the implementation of your method, [self takeObjectValueFrom:self->XXX];, where XXX is the name of your special outlet.

That's it.

----
**How do I connect menu items to actions in my document (in another .nib)?**

(Assuming the actions to which you want to connect will form part of the ResponderChain - the easy way to do this is to put them in your subclass of NSDocument or NSWindowController)

1. Select FirstResponder in the Instances tab of the main InterfaceBuilder window.

2. Select the Classes tab in the same window.

3. In the Inspector (keyboard shortcut: Command-1), add your action(s) to FirstResponder.

4. Go back to the Instances tab in the main InterfaceBuilder window.

3. Now connect your menu item(s) to FirstResponder, selecting the action(s) you have just created.

see also MenusAndDocuments

see also MakingNibsTalkToEachOther

----
**Can I create interface elements in IB without making them subviews of a window or panel?**

Yes. Make them subviews of a custom view object.

