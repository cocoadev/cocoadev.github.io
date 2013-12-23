---
layout: page
title: DirectoryOpenPanel
---

To set an open panel to only select directories, do the following:

    
NSOpenPanel *openPanel = [NSOpenPanel openPanel];
// That has got to be one of the most repetitive Cocoa lines of code ;-)

[openPanel setCanChooseDirectories:YES];
[openPanel setCanCreateDirectories:YES]; // Added by DustinVoss
[openPanel setPrompt:@"Choose folder"]; // Should be localized
[openPanel setCanChooseFiles:NO];


-- DavidRemahl

  *It'd be even funnier if it had an instance method "openPanel" to actually, well, open the panel ...*

*and a method     -(BOOL)openPanel which returned whether the shared NSOpenPanel instance had a panel open.... OK, that's stretching it.*

That's just silly - openPanel would already exist, so you'd want to ask if -(BOOL)panelOpen. ;-)

