---
layout: page
title: PropertyListHelp
---

I have an app that stores it's NSTableView information in a .plist PropertyList. But it won't work because of a image colomn. Any Help?

----

You'll want to encode the image, or discard with it altogether. The image can probably be encoded in an NSData instance.

