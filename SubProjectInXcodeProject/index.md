---
layout: page
title: SubProjectInXcodeProject
---

I am trying to figure out how to create a Sub Project.  Let me explain the situation:


* I have the main project.
* My project uses bundles as "plugins" of sorts.
* The main project will include as least one plugin as a "core" plugin.
* I want this core plugin to be as fully integrated into the project as possible.  The ideal solution is that all sources/resources would be in the same project file but that it would be built as the separate bundle as a build step before the main program is compiled/linked/etc.


Should I be using a new target or something here?  I'm not too sure how to use targets so I could use some insight on that...

Thanks.
