---
layout: page
title: NSWindowControllerLoadsWrongLocalizedNib
---



I have been developping an app in English and just started localizing it in French.
My MainMenu.nib loads the French version correctly (I have set my International perferences to do so), but any new window created thru :

    [NSBundle loadNibNamed:@"NewManualGroupSheet" owner:windowController];


does not load the French version, but the English one.
I have looked all over but didn't find anything...

