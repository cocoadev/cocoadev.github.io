---
layout: page
title: KeyTranslate
---



Hi,

I have a set of pre-defined Keycodes and I'd like to display the string value dependent on the language that is selected. Empirically I can determine that a keycode of 0 is the letter "a" in the U.S. language and that it's the letter "q" in French. In short for a given keycode and language how do I determine in advance the stringvalue of the keystroke that will be posted?

I read about how there used to be this function called KeyTranslate ( http://developer.apple.com/documentation/Carbon/Reference/Event_Manager/Reference/reference.html#//apple_ref/doc/c_ref/KeyTranslate and http://developer.apple.com/documentation/mac/Toolbox/Toolbox-78.html ) which lets you do exactly what I want but the notes clearly state that the information is deprecated. 

Pointers to new information would be most welcomed!!

----

See the discussion on GetKeys. It's closely-related.

