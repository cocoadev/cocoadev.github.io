---
layout: page
title: HowtoobtaintheFATserialnumberfromavolume
---

I would like to retrieve the FAT serial number from a volume mounted on Mac OS X  from a C program.  Here is how one can do it from the command line.

dd if=/dev/disk4s1 skip=67 bs=1 count=4 | hexdump -v -e '1/1 "%02X" " "'

I have not found how to do this with the CoreFoundation classes.  I also need to find this information out as a regular user not a privileged user or root.

If anybody can point me in the right direction I would appreciate it.

Thanks,
Chris

----

This is not a forum. It's a wiki. You wouldn't post a "what is a dog?" question on Wikipedia, would you?

----
I planned on suppling the answer once I found it out. But if this is not proper then please delete.

