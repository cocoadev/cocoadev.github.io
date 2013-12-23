---
layout: page
title: ObjectiveCModuleName
---

Before I ship software, I do a "strings" command on the binary.
If you've never used it before, "strings" is a command-line that prints out anything it finds that looks like a string in a binary file.

I do this to make sure I haven't left anything in there by mistake, like forgotten debugging printfs.

One thing I've noticed is that the full paths to my Objective-C files are ending up in the binary. (Even after running strip.)

Further investigation showed that this is in the Objective-C section of the file.  If you to an otool -vo on the file the path is showing up as the name field of the Objective-C module.  (Yes, it's there in Apple's programs too.)

It's no big deal really but for some reason it bothers me that the path name is ending up in the file. What if I had called the file something embarrasing or my hard disk was named something that I otherwise wouldn't want showing up in my binary? ;)

Does anyone know a way around this?  Again, no big deal... Just an interesting thing.

