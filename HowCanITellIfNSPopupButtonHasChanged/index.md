---
layout: page
title: HowCanITellIfNSPopupButtonHasChanged
---

NSPopupButton does not seem have delegate methods.  I need to know when the user has clicked on it and changed their selection.

If it makes it any easier, it's bound to the selection/selection index of an NSArrayController.  It correctly changes the array controller, etc. but I also need something else to happen to the same window whenever the selection of the NSArrayController changes.  It doesn't look like NSArrayController has delegate methods, either.

I've been able to accomplish what I want with an NSComboBox (I just trap the comboBoxSelectionDidChange notification and force my NSArrayController to choose a new index based on the index of the combo box, as well I can now update my other window element when this happens.

But...

I don't like the combo box for this application.  I simply want a grey-background list (just like NSPopupButton looks like).  If I could make the combo box look exactly like the popup button, I would use it instead.

Anyone have any ideas for me?  I'm sure I'm just missing something obvious.

Thanks,

KentSignorini

----

NSPopupButton acts as a NSButton (wich is its ancestor). An action message is sent to target object each time NSPopupButton is changed.

In IB, simply connect a "line" between your controller class instance and NSPopupButton object on window, then select action you want and type Connect.
You also can do this in program by setTarget/setAction methods.

Bru

----

Wow.  Oh man do I feel stupid.  NSPopupBUTTON!  YEESH!

Thanks.

(shakes head and crawls humbly away)

KentSignorini

----

The 'new school' way of accomplishing this is to bind a key in your controller to one of the NSArrayController's selection bindings (i.e.     selection,     selectedObjects,     selectionIndex,     selectionIndexes).  So, for example, you could add the following in your Window Controller:
    
- (void)awakeFromNib
{
	[self bind:@"mySelectedObjects" toObject:myArrayController forKeyPath:@"selectedObjects" options:nil];
}
- (void)dealloc
{
	[self unbind:@"mySelectedObjects"];
	[super dealloc];
}

and then implement the corresponding 'getter' and 'setter' methods, (    - (NSArray*)mySelectedObjects; and     - (void)setMySelectedObjects:(NSArray*)inNewSelectedObjs; in this example) and they'll get called whenever the selection changes.  This is a bit more fiddly than just hooking up the action, but it has the advantage that it'll work regardless of how the selection is changed, while the NSPopUpButton action method will only get called if the user specifically clicks on it and chooses an item.  -- Bo

