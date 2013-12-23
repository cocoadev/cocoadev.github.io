---
layout: page
title: NSArrayControllerWithComboBoxesEtc
---

I hope someone can help me.  I have a two part question here:

**PART 1**

I am using an NSArrayController with an array of objects I've created.  To make this simpler, I'll only mention the required object variables.  The object is called Combatant and each one has the following:

*name
*weapon 1
*weapon 2
*weapon 3
*weapon 1 damage
*weapon 2 damage
*weapon 3 damage
*shield rating

Now I have a section of my document that includes an NSComboBox.  In that combo box I want the data to fill with the names of the three weapons for the currently chosen combatant.

THEN, for whichever weapon is chosen, another NSTextField will display that chosen weapon's damage rating.

Is this difficult to do?  How would I do it?

----

Personally, I never used Combo Box yet. But you should check the docs at Apple about NSComboBox : it seems that you have two choices of implementation. Either you setup a dataSource, which I think is slightly more tricky, but when you get it right, you don't need many more lines of code. The other solution would be to do it "by hand", adding every weapon of your array, one after another, using addItemWithObjectValue, for example.

And for the other NSTextField, you'll probably need a delegate to ensure that everytime you change selection in your NSComboBox, a method is called to change the NSTextField as well. And then, depending of the selection, it decides what to display in the NSTextField. Something like this :
    
- (void)comboBoxSelectionDidChange:(NSNotification *)notification
{
    [myTextField setIntValue: [ [myArray objectAtIndex: [myComboBox indexOfSelectedItem]+3] intValue] ];
}


So, everytime you select something, your NSTextField will display the corresponding damage ratio. If you have more than 3 weapons, you'll need to change the +3 accordingly. In the example, I assume that your weapon damage ratios are NSNumbers. Hope I did not confuse you even more...

-- Trax

----

I believe he wants to use NSArrayController for this. It seems to me that you can just make an array property containing the weapon names, and bind another array controller to the current selection's weapon-name array property, and then bind the combo-box's *contentValues* property to the weapon-name array controller. �DustinVoss

----

**PART 2**

In another NSComboBox in my document, I want the data to be the names of all the combatants controlled by the NSArrayController EXCEPT the one that's currently selected.

Now, based on the name chosen in that combo box, another NSTextField will display this second combatant's shield rating variable value.

Any suggestions for this?

Thanks for any help you can provide.

Kent!

----

You could make and maintain an array containing all the non-selected combatants, but I think there must be a better solution. �DustinVoss

----

Using a dictionary would be a potable solution. To each character corresponds a dictionary. You simply need to associate each property (name, weapon 1, etc...) to a value, number of string. It doesn't answer the question directly, but maybe it would simplify things... -- Trax

