---
layout: page
title: VolumeInfo
---



Maybe I'm just extremely short-sighted here, but how do I figure the total capacity of a volume? Ok, I know how to get mounted disks and get the ones I want, I also get the correct bytevalue for FREE space left on a volume (through the General/NSDictionary object containing General/FileSystemAttributes, key: General/NSFileSystemFreeSize). Now, there exists a filesystem attribute with key: General/NSFileSystemSize, which I thought would return the total capacity in bytes. It returns a value for sure, but the value is not in bytes, comparing the result divided by 1024/1024 to the Finder Info on the volume. Is the value incorrect? Or is it returned in some other form than bytes? Do I need to use a different route to get to what I want?

-- Frode Danielsen

----

Ok. I'm truly sorry. That was a stupid question. But for what it's worth, maybe there exists another person with the same problem. All I did wrong was insert the value in a variable with a type to small for the bytevalue. I used a simple long, now I use an unsigned long long and it works.

If anyone feel this doesn't need to be here, just delete it.

-- Frode Danielsen

----

Sounds like you're making a Finder replacement... or something similar.

-- General/RobRix

----

Frode you are correct.
In the example from apple there is a bug.
They are using a unsigned long to store the size but this is to small (<4GB)
Use indeed the unsigned long long and all is correct!

-- Jack
