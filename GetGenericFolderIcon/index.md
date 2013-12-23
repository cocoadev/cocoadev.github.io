---
layout: page
title: GetGenericFolderIcon
---

To get a generic folder icon you have to use the NSWorkSpace API as follows:
    
            NSString *folderFileType = NSFileTypeForHFSTypeCode(kGenericFolderIcon);
            icon = [[NSWorkspace sharedWorkspace] iconForFileType:folderFileType];


You can generate other system wide icons by using the following HFS type codes:

* kGenericFolderIcon
*  kDropFolderIcon
*   kMountedFolderIcon
*   kOpenFolderIcon
*   kOwnedFolderIcon
*   kPrivateFolderIcon
*   kSharedFolderIcon

