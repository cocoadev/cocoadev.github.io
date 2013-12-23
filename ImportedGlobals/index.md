---
layout: page
title: ImportedGlobals
---

Now back in the day, you could stuff a whole bunch of **const VAR_TYPE $VARNAME** stuff into global space of a .h file, import that file and have all those yummy globals consts.  How do we do that in Objective-C?

----

The same way. You could, for instance, put it in your prefix header...

    const int SomeValue = ...;

Me, I'd rather use macros.

-- RobRix

