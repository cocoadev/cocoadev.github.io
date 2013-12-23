---
layout: page
title: WhatIsThePrivatePathPrefix
---

The system returns /private/tmp as part of the path when files are located in /tmp, but calling     stringByStandardizingPath will remove it.

What is the point of this "private" directory? Is it best to remove it (using     stringByStandardizingPath) when e.g. saving references to files?

----

/private is the *real* location of /tmp. In other words, "/tmp" is a symbolic link to "/private/tmp". The documentation for     stringByStandardizingPath clearly states that the method removes the "/private" reference specifically. Although I would not save a reference to something in /tmp or /private/tmp, yes I would run a path through     stringByStandardizingPath before saving it somewhere. By doing so, you reduce the risk of an altered symbolic link messing you up.

----

"private" exists purely for legacy reasons. I wouldn't be entirely surprised if it went away at some point in the future. -- General/FinlayDobbie

----

Slightly off topic but probably good to mention: when saving references to files, consider saving an alias record rather than a path. That way if the user moves the file and your application tries to access it later, it will still work properly. Aliases are not supported in Cocoa but there are various Objective-C wrappers around the necessary Carbon functions.

*It could be argued that if a user is moving hidden files in /tmp, they know precisely what they're doing, and following an alias would just frustrate them*

----

worth noting: General/NSTemporaryDirectory() (on Panther, at least) returns a path that includes the /private prefix. *--boredzo*
