---
layout: page
title: SourcePathStingsInExecutable
---

I've discovered a rather annoying thing that possibly only happens in Xcode 2.x and above: for some reason, the paths of my source files are embedded in the final executable (deployment style), and I've seen other applications out there (presumably compiled by a similar version of Xcode) that also has them. You can easily check for them by running     strings against the executable file. These are not string I've created or use in my app.

Is there any way to get rid of that? I've tried but haven't seen any options that will do it, and I don't see their use in the code. (NOTE: READ CAREFULLY - this is not a debug-setting problem; this is an executable in deployment mode with all debug symbols stripped out [not that paths would be debug symbols anyways]).

