---
layout: page
title: SetFilePermission
---

 I need to unset the read only flag on a file that is on a network mount.  chflags is not supported for net mounts (of the samba kind anyway) chmod will not work either. any suggestions?

----

Have you tried setmode()?

*Or, more specifically, open your terminal and type (without quotes) "man setmode" ... this will tell you everything you *should* need to know. ;-) Though examples are what makes this site nice, aren't they? Hint hint, guys/gals?*

