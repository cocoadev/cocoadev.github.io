---
layout: page
title: NSKeyedArchiverFormat
---

Hi,

I've noticed that NSKeyed archive are *nearly* human readable: open such an archive with plist editor, you'll see it can be interpreted as an NSDictionary with various (standard+custom)keys and values.

Does any body already have really understood this format? is it documented? does any body even have written a small program that list the used keys, the class name etc ?

for the people who will ask "why do you want to do that ??" : i want to reverse engineer some Apple archives to see what class is archived inside, and what does it contain. I can then maybe unarchive them myself by reimplementing the archived classes.

thanks !

----

Raise your hand if you didn't realize plist editor could read binary plists. *raises hand*

