---
layout: page
title: NSNotificationQueue
---

NSNotificationQueue takes notifications, coalesces and prioritizes them, and sends them to an NSNotificationCenter. You may have multiple NSNotificationQueues referring to one NSNotificationCenter.

They can be very useful for filtering lots of messages down to essential information. They are also good for use in drawing operations and event handling, as they return right after queueing the notification (I do not know if queueing the message itself entails an NSLock), and thus do not slow down the operation much.

