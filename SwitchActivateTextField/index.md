---
layout: page
title: SwitchActivateTextField
---



Probably a no-brainer: this is probably covered about a hundred different places on the web, but I don't know what I'm supposed to be looking for.

How do I create a General/NSButton that if clicked activates a related General/NSTextField; example: I have three options {cats, dogs, other}, if other is selected, I want the user to be able to enter a value in a field that is greyed out unless "other" is selected.

Thanks!

R.

----

Well, you *could* write a controller object that activates/deactivates the text field, and set the button's action (in General/InterfaceBuilder) to call this method. (Check out General/ModelViewController if you don't know the general paradigm already.)

----

Step one - open Interface builder and drag the appropriate controls into place.
Step two - wire them up to your controller (whether a generic controller or and General/NSDocument subclass)
Step three - When the checkbox fires its action method (defined in step two - read up on it), set the General/NSTextField's enabled value to match the checkbox's 'state' value (checked would equal yes, so checking the box means the text field is enabled, and vice-versa ([sender state] if you need a hint).
Step four - add nice touches such as clearing the contents of the text field if "not sender state", meaning clear the text if the box is unchecked.

There are a million resources - this site is one of them. Also, picking up a good Cocoa book will work wonders. This site has a list of them, though I'm not searching for it for you. ;-)
