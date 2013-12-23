---
layout: page
title: NSOpenPanelSetAllowedFileTypes
---

    
...
NSOpenPanel *open = [NSOpenPanel openPanel];
[open setAllowedFileTypes:[NSArray arrayWithObject:@"md5"]];
[open setAllowsOtherFileTypes:FALSE];
[open runModal....];
...

this still allows me to select any filetype i want. what am i doing wrong here? don't quite understand it..

thanks!

oddysseey

----
This is working for me, hope you can use it as well.
    
...
NSOpenPanel *openPanel = [NSOpenPanel openPanel];
[openPanel setAllowedFileTypes:[NSArray arrayWithObject:@"md5"]];
[openPanel setAllowsMultipleSelection:NO];
[openPanel setAllowsOtherFileTypes:NO];
[openPanel setCanCreateDirectories:NO];
[openPanel setCanChooseDirectories:NO];
[openPanel setMessage:NSLocalizedString(@"ChooseMD5File",@"")];
[openPanel beginSheetForDirectory:NSHomeDirectory() file:nil types:[NSArray arrayWithObject:@"md5"] modalForWindow:mainWindow modalDelegate:self didEndSelector:@selector(openPanel1DidEnd:returnCode:contextInfo:) contextInfo:nil];
...

have fun. take care.

oddysseey

