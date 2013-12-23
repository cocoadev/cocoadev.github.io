---
layout: page
title: CoProcess
---

An alternative to multi-threading.  You write the parts that you want to be processed simultaneously as either a seperate command-line tool and create a process to run in the background, communicating through pipes, or you can also communicate through DistributedObjects, UnixSockets (or NetworkSockets... they are different), and any other of the plethora of interprocess communication models supported by unices.  You can also write your app to act in one of these sub-process modes given a command line option which you pass to it in starting it.

One major well known project that uses CoProcess<nowiki/>es instead of MultiThreading is the webserver Apache.

IoLanguage uses CoRoutine<nowiki/>s for its concurrency. It's quite nice, implemented with the @ and @@ operators, and also implementing FutureObject<nowiki/>s, which is very cool.

