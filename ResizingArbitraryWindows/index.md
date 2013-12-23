---
layout: page
title: ResizingArbitraryWindows
---



We're working on a utility that needs to resize arbitrary windows (i.e. windows not owned by the utility).  We know about the CGS private functions and have dug up CGSSetWindowShape, but we're not convinced that's the solution.  Does anybody have any pointers on this subject?

Also, if CGSSetWindowShape is the way to go then does anybody know anything about the CGSRegionObj type?

Thanks
- NateGray

----
You should probably look into the Accessibility APIs.

----
Thanks!  That did the trick!

