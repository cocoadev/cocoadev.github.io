---
layout: page
title: IBpaletteAndPopUpButton
---

I have palettized one PopUpButton. But i could not able to get menu items when it is double clicked (popUpButton has not been put in Test Interface mode) as like NSpopUpButton. 
----
Did your pop-up button have menu items in the palette's nib file? Seems like if it's a subclass of NSPopUpButton it would get the same field editor automatically. --JediKnil
----
I have subclassed NSButton. I need to get the field editor for it.
----
Ah, well, NSButton uses a different field editor. So, somehow you need to get the field editor for NSPopUpButton. You can find this out by putting     [[[[NSPopUpButton alloc] init] autorelease] editorClassName] somewhere in your plugin (it has to be in the context of IB). If you print this to the Console, you can then implement editorClassName yourself to return this. --JediKnil

