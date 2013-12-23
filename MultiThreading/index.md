---
layout: page
title: MultiThreading
---



AnApplication which uses MultiThreading is capable of doing multiple things at once without them necessarily being synchronized. MultiThreading is considered an effective way of UpdatingDisplayDuringTimeConsumingTasks.

In order to use MultiThreading in your application, you use the NSThread class, and tell it to detach a new thread with a specified method. Then it is up to you to set up a local AutoReleasePool for the thread. The thread will continue to exist until the method returns.

If you want to have the thread communicating, you have to also understand quite well what NSConnection and RunLoop are, and these are quite complex if you never heard of them before. In other words, except for very simple cases, multithreading requires many other things to be under control than just NSThread.

----
**MultiThreading topics:**
[Topic]

Put **\\%\\%BEGINENTRY\\%\\%MultiThreading**[:section]**\\%\\%ENDENTRY\\%\\%** on other pages to add them to this list. You do *not* need to edit this page.

