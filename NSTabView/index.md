---
layout: page
title: NSTabView
---

NSTabView explanation...

----

Q: How do I activate an NSTabView programmatically? Let's say I've run a script and want to take the user to the Log tab automatically if failed.

A: If you want to go to the second NSTabViewItem you can call [tabView selectTabViewItem:[tabView tabViewItemAtIndex:0]] .

----

Q: How do I disable an entire NSTabView?

A: IMO, you shouldn't. Disable the controls inside the tab view. This way the user can look at the tabs and see what options would be available to them if they enable the ruling option.

----

Q: How do I add a tab to a tab view in IB?

A: One way is to click on an existing tab and copy, then paste. A new tab appears.

or increment "Number of Items" in the Attributes pane of the Info window.

----

Q: How do I rearrange tabs in a tab view in IB?



A: Click on the tab you want to move then use the left and right arrow keys to change its position.
\
----

Q: How do I dynamically change the control size of a tab view, programmatically? Calling setControlSize: causes funniness.

A: . . .

----

Q: How can I add a NSTabViewItem to a tab view, programmatically? It seems the API are not working as you would think they are.

A: The discussion about FirstResponderSetAfterDelay has a method named newTab: to do it.

----

Q: How do I hide a tab view?

A: HidingTabViewItems

----

Q: Several tab views are using left and right sided tab views now, but the documentation claims this will not yet work (experimentation confirms this). How exactly are they getting this effect?

A: This is now possible within Interface Builder, I believe beginning with the Developer Tools included with Jaguar. Simply drag an NSTabView to a window, select it if necessary, open up it's attributes inspector, and choose one of 4 positions under the popup menu named "Direction" and you're done!

----

Q: Are there any examples of how to do a sliding tab view like there is in Project Builder?

A: I'm going to guess the answer is no, or, not without a lot of custom work on your own. I say this because if you run PB using a non-aqua theme, you'll see PB is still using the default aqua style tabs -- which means PB is drawing the tabs itself.

----

NSTabView has different tab view types and may be displayed without tabs. This is useful for displaying different views in one window, e.g. switching between an NSBrowser and an NSOutlineView for viewing objects. Each may be defined on a separate tab and the application may switch between the tab views.

----

The NSTabView is how you can easily create a System Preferences style preferences for your application. Simply create X number of tabs, and then call a window resize function based on an element inside the tab view (like an NSBox's frame size). Make sure though that you have tabs visible turned off and that you have the tab pushed to the very top of the NSWindow so as to leave maximum space for your user interface elements. To change the views, get the NSToolbar to change the visible tab. Of course, resizing the window would be helpful and if you really want to, make the tabview switch to a blank tab as the window is resizing to get the Sys Prefs effect.

takeSelectedTabViewItemFromSender: is handy for this -- DustinVoss

----

Q: How do I unpack a tab view? I know there is a menu item in IB for this, and IB Help says select the container and unpack, but the unpack menu item stays grayed out even when I am sure I have specifically selected the tab view (as evidenced by the title of the inspector window).  Is there some secret to this?

A: 

----

Q: How can I trigger an alert when a given tab item is selected?

Here is my code for the method:

- (IBAction)checkTabs:(id)sender
{
	int	tabSelection;
	
	// - (void)tabView:(NSTabView *)tabView didSelectTabViewItem:(NSTabViewItem *)tabViewItem
	
	tabSelection = [myTabs indexOfTabViewItem:[myTabs selectedTabViewItem]];
	
	if (tabSelection == 0)
	{
		[self alertRadioOne];
		NSLog(@"Tab \"Buttons\" was selected.");
	}
	else if (tabSelection == 1)
	{
		NSLog(@"Tab \"Widgets\" was selected.");
		[self alertRadioTwo];
	}
	
	else if (tabSelection == 2)
	{
		NSLog(@"Tab \"Test\" was selected.");
		[self alertRadioTwo];
	}
}

Thanks for any and all assistance!

A:

----

Q: When creating an NSTableView based off of an NSArrayController, you can easily add buttons to add/remove items by binding the actions to the Array Controller.  You can do this with tabs easily as well, binding selectNext/Prev to the button.  However, I could not find a similar feature a the "canAdd" and "canRemove" that ArrayControllers provide to enable/disable buttons.  With the Add/Remove buttons and an ArrayController, you can very easily enable/disable those buttons by binding the Enable binding to the ArrayController.  Does such a thing exist for NSTabView?  ie. "canSelectPrevious" or "canSelectNext"?

