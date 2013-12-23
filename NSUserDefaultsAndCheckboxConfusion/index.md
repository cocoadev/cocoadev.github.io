---
layout: page
title: NSUserDefaultsAndCheckboxConfusion
---




I have things set so a checkbox in my app toggles a preference and writes it to the prefs. I tried setting this checkbox's state at startup according to the prefs file with NSUserDefaults, and it works, except that the checkbox gets turned on even when the preference is set to off. Is there something wrong with my code? Here it is:


    
if( [defaults boolForKey: @"transparentEnabled"] == NO ) {
		[transparentCheckbox setState:NSOnState];
				
	} else if( [defaults boolForKey: @"transparentEnabled"] == YES ) {
		[transparentCheckbox setState:NSOffState];
	}


Also, before someone mentions it, switching YES/NO's positions just produces the opposite effect; the checkbox is off no matter what.



Any idea of what's wrong? Thanks!
----
It's much easier to simply bind the check box to NSUserDefaults in InterfaceBuilder -- bind the     value setting on the check box, to     transparentEnabled in NSUserDefaults (you can use a value transformer to negate it if necessary). However, most likely you never actually set the key     @"transparentEnabled", or you misspell it when setting it, because     NO is the default value for     boolForKey:. Good luck... --JediKnil
----

This is probably not your problem, but you should *never* directly compare a     BOOL with     YES or     TRUE. A     BOOL is actually
 typedef'd as a
    signed char, which has 256 possible values. Zero is used to mean "false", and all of the other 255 values mean "true".     YES 
is defined as 1, so if you happen to have a     BOOL which contains -1 or 42 or any other such number, comparing     myBool == YES will be 
false, even though     myBool is true.

