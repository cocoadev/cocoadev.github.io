---
layout: page
title: DriveNotificationsWithoutNSWorkspace
---

I currently use NSWorkspace's notification center to get notified when an iPod is connected or disconnected. This works for FireWire and USB iPods. However, this method gets called other times too when the iPod/disk isn't actually getting disconnected or connected (just mounted/unmounted). Is there another way (IOKit?) to get drive notifications? Thanks.

----

DiskArbitration. *--boredzo*

