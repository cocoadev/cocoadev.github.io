---
layout: page
title: WriteToSocketInBackground
---



I am writing a server that needs to be able to answer multiple requests asynchronously ... what is the best approach for this?

I can't seem to be able to share the General/NSFileHandle amongst threads (the acceptConnectionInBackgroundAndNotify in the main thread General/RunLoop seems to interfere with my use in a separate thread?)... and using writeData from the main thread causes a block (the client has a limited receive buffer).

I've been looking for this info everywhere, to no avail. It would be nice to have a writeInBackgroundAndNotify: method!

----
General/AsyncSocket has one o' those.

----
General/NSFileHandle is documented as not being thread safe, so it's no surprise it falls down when you try to use it on different threads.

If you don't want to switch to a different class, like General/AsyncSocket or General/NSStream, then you can always get the file descriptor from your General/NSFileHandle and then use low-level UNIX calls like     write and     select to do your work.
