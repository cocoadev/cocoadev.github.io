---
layout: page
title: UKQueueFileRename
---

Does anyone know how I can use General/UKKQueue to get the new path of the watched object? I put a path into the queue and get the notifications if the file was renamed, but it doesnt give me the new name of the file. Does anyone know how to track a file if it is moved or renamed? Thanks.

----

To track files, you need to use General/FSRef from the Carbon framework.

----
Note that General/FSRef will *not* follow a file across renames on all filesystems. It will work fine on HFS+ and many other filesystems, but it's not universally supported.
