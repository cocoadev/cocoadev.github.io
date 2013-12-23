---
layout: page
title: WindowAlwaysInFront
---

for other window level constants see General/NSWindowLevel

I want to make a certain window always! always! always! always! always! (is that enough times for you?) on top
like the Special Character Palette, I want it to be always on top even when other applications are active.
Even if work is being done on another program.

I've tried all the regular suggestions:  making it an General/NSPanel, and making it a utility window;

I've tried using the following code:

    
[myWindow setHidesOnDeactivate: NO];
[myWindow setFloatingPanel:YES];
[myWindow setLevel:General/NSFloatingWindowLevel];
[myWindow makeKeyAndOrderFront:self];


none of these things seems to do what I want

----

    [window setLevel:General/NSFloatingWindowLevel];

--zootbobbalu

----

The window will need to be a General/NSPanel utility window in order to always visually appear active. You'll also need to set the panel's setHidesOnDeactivate to NO in code.

----

Also try General/NSScreenSaverWindowLevel or General/NSStatusWindowLevel

*You can also make it a higher level by adding to it. Such as General/NSScreenSaverWindowLevel+1.*
