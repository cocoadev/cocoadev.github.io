---
layout: page
title: HowToRenameAVolume
---

Is there a way to rename a mounted volume programatically?

----
The disktool command line utility has a '-n' switch for renaming volumes ... Perhaps a simple NSTask wrapper?

----

NSAppleScript would do the trick as well.

    
tell application "System Events"
	set (name of disk 1) to "Edward"
end tell


*... as long as you remember to catch and handle any errors ...*

----

I tried using disktool, and it works, but it sits open rather than quitting after it renames the disk... ???

Why must errors be caught for AppleScript? Its a removable flash drive... Just wondering?

*Because if the rename doesn't take place for whatever reason (and there can always be reasons - they're users!), your app should reflect that rather than assuming everything's okay. Good practice. ;-)*

----

Ah! My app is nice like that, it goes and finds the volume after renaming it :) Luckily I have something to search by.

**If you catch the error first, you won't have the problem of your app finding a different volume that happened to be renamed at the same time, and thinking all went well.**

----

No, no, my app looks for a specific device identified by a few folders and files. It's removable media. Sort of like iPod.

----

Unfortunately you're still not quite getting the point. ;-) This is evil. Sh** happens, especially out in the wild. Take my word for it. ;-) Best to handle errors properly.

