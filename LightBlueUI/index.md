---
layout: page
title: LightBlueUI
---

I'd like to do a tabbed window with a light blue background, instead of the standard background or brushed metal.

Is there a way to do this?

----

    
NSWindow *myWindow = ...;
NSColor *myLightBlueColor = ...;
[myWindow setBackgroundColor: myLightBlueColor];


----
Ah, thanks.

Do you know how to use my own graphics for the tabs, and changing the alternating colors for tables?

----

You'll basically have to subclass the existing control classes and override their drawing methods. However, this is a bad idea in most cases. Users expect Mac apps to look like Mac apps, not some crazy blue thingy where every control is different. That's reserved for MicrosoftOffice.

----

Or Disco. -- RobRix

----

Well, Disco's more grey than blue. :P -JonathanGrynspan

