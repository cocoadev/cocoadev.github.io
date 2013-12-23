---
layout: page
title: TextDidEndEditingCatchModifierFlags
---



I�m using this notification to know if the return key caused end editing:

    
- (void)textDidEndEditing:(General/NSNotification *)aNotification
{
    if (General/[aNotification userInfo] valueForKey:@"[[NSTextMovement"] intValue] == General/NSReturnTextMovement)
	 { � }
    else [super textDidEndEditing:aNotification];
}


But I�d like to know whether the shift modifier key was also pressed�

Unlike for Tab and General/BackTab (=Shift+Tab), there�s no constant for shift+return; is it possible to get the modifier flags in textDidEndEditing?

----

Try examining     General/[[NSApp currentEvent] modifierFlags].
