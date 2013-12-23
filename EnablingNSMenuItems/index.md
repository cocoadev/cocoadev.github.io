---
layout: page
title: EnablingNSMenuItems
---

I've got a few menus in my current project that I'd like to disable and enable at certain points. Right now, I've got the MSMenuItem entries for those menus as outlets from my code, and I can turn them off with setEnabled easily enough. Once blanked out, though, they never light up or activate again after using setEnabled(true), although the key equivalents still work.

What am I doing wrong?

----

You shouldn't manually enable and disable the items. Instead implement     validateMenuItem: in the target to return YES/NO, depending on whether or not the selector for     [menuItem action] is possible to call.

See NSMenuValidation for more info.

