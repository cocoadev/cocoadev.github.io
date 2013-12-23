---
layout: page
title: NSFolderImage
---

I am creating a source list ala iTunes/Mail/current fashionable look and have hit a snag with icons. Interface Builder on Leopard gives free icons for burnable and smart folders via [NSImage imagenamed:] but does not provide one for a standard folder nor home folder! 

Can anyone please advise if there is one available, or failing that, where I might find the standard folder and home folder images on my computer so as to be able to copy them into my application bundle?

----

You can use NSWorkspace's     -iconForFileType: to get this image. Admittedly it's a bit less elegant than it needs to be, but it should work. --JediKnil
    
NSImage *folderIcon = [[NSWorkspace sharedWorkspace] iconForFileType:NSFileTypeForHFSTypeCode(kGenericFolderIcon)];


----

Many thanks, that works a treat. I honestly don't think I'd ever have spotted that way of doing it. Seems very strange to provide NSFolderSmart and NSFolderBurnable but not "NSFolderStandard" or similar.

----

Try grabbing "GenericFolderIcon" from /System/Library/CoreServices/CoreTypes.bundle  There's lots of other goodies in there too.
- Harvey

----

Rooting around in bundles that are Not Yours is never preferable when there's explicit API to do it.  Since the filesystem knows about folders, it makes sense that the filesystem-related API would be able to retrieve icons for folders as well as files.  Since the concepts of "smart" and "burnable" folders don't exist at the FS level, they're introduced elsewhere in the framework.  --K

----

You can also retrieve other fourchar file type icons using the above method. Try kOpenFolderIcon, kFullTrashIcon, or kDesktopIcon (for instance.)

