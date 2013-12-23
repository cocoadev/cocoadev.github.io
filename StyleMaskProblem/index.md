---
layout: page
title: StyleMaskProblem
---


Ive tried to set General/NSBorderlessWindowMask (styleMask value) when i create an General/NSPanel.
After that ive set setFloatingPanel:YES because i need a borderless panel which stays on
top of all other windows. This is not possible!
Is there a trick to do that?

Would be great to get help on this topic.

Thanks, Roger.


----

Works for me. What are you actually doing? General/PostYourCode.

Are you just instantiating an General/NSPanel? Or subclassing it? Either way, maybe you need to call      - [<thePanel> setLevel:General/NSFloatingWindowLevel]; --GC
