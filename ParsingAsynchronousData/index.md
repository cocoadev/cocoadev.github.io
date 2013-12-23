---
layout: page
title: ParsingAsynchronousData
---

With the help of CocoaDev folks, I finished a program that watches the USB port for a serial device, then it creates notifications when data are sent from the distant device. The data are 2-digit numbers, and they trigger a QuickTime movie to play or stop, depending on the first digit (which movie) and the second (play or stop). Unfortunately, in the "Real World," sometimes the movie keeps playing despite the fact that the electrical contact (the trigger to play the movie) has been opened. Since the hardware A->D converter only sends an "open/closed" circuit message (via a serial to USB converter), I can't recover from the loss of an event such as "open circuit."

I'm using a fileHandle and readInBackgroundAndNotify to extract the serial stream from the USB port. Is this an efficient way to capture asynchronous data? Should I use a different method (readToEndOfFileInBackgroundAndNotify)? Are any of these methods superior for making sure that no data are lost? Perhaps there is a better way? I'd appreciate any advice that might be out there!

-dan
(Amended 6/24/04 because I obviously wasn't thinking when I posted the original...)
----

I use a tiny c serial IO library I got from freshmeat a few years ago -- I originally used it under linux but it seems to work fine under Darwin. The project is no longer alive ( it seems gone, altogether, actually ). I can send it to you if you want. It works fine with my Keyspan USB->serial converter.

Anyway, what I've done is use a thread to poll it for data. Yes, that's crude, but I've had ( as far as I can tell ) 0% data loss. It just works. If you want I can send you my code -- however, my code's really more of a user-space driver API for cheapo hobby robotics electronics ( SSC12, SV203, BrainStem, etc ). 

Oh, and it's all c++. Sorry.

--ShamylZakariya

If you're after the lowest latency, the best way is probably to use a blocking API such as NSFileHandle's -availableData, or a simple POSIX read(), in a separate thread. This avoids polling, and avoids failing to read data because you're in the middle of a modal session and the notification can't get posted.

