---
layout: page
title: IconForBundleType
---

It's easy enough to get the icon for a particular file type using:
    
NSString *type = @"html";
NSImage *icon = [[NSWorkspace sharedWorkspace] iconForFileType:type];


But, this technique doesn't seem to work for bundle types (i.e. app,prefpane,plugin...), I don't want to have to write code as follows  with a case for every known bundle type:
    
NSString *type = ...
if([type isEqual:@"app"]) 
    type = NSFileTypeForHFSTypeCode('APPL');
else ... //other cases	
    type = NSFileTypeForHFSTypeCode('fldr');	
NSImage *icon = [[NSWorkspace sharedWorkspace] iconForFileType:type];


Can someone suggest an alternative? Note: the file doesn't necessary exist, so the normal NSWorkspace IconForFile: won't help.

