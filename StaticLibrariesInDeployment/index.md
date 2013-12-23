---
layout: page
title: StaticLibrariesInDeployment
---



I struggled with this for a long time, and the solution was a bit hard to find elsewhere in General/CocoaDev, so I thought I'd add a separate topic.

If you are creating your first tool with static libraries, and ready to make the final app, here are some important tips.

1) Switch from Development to Deployment phase. Get there by the Project > Info window (select the project first) > Styles pane. This will turn General/ZeroLink off (there are other topics on this) which will build your app as a standalone program so you can distribute it with the static libraries built in.

2) You may have to add an -L<dir> flag to the "Other Linker Flags" line in the Project Info to tell Xcode where to find your static libraries. If the libraries have been added into the frameworks section (Groups & Files), you won't need the -l<lib.a> flags.

3) (This was my big showstopper! Rrr!) If you get a cryptic "undefined symbols" or other errors that were not present in previous builds (Development phase), you have linker errors. These can crop up because deployment phase linking is inherently different from development. To see the specific errors, bring up the build log (Build > Show Detailed Build Results) and drag up the lower pane in the window to see the command line specifics. That will tell you what to fix to get your program to build completely.

Can't believe it was so dang obscure to figure this out!

seal68
