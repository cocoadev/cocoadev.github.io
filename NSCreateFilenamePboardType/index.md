---
layout: page
title: NSCreateFilenamePboardType
---

I just found NSCreateFilenamePboardType in NSPasteboard.h but also realised it is partly described in the Functions section of the AppKit documentation.

So if I only accept e.g. png pictures to be dropped on my program, I would use it with "png" and filter on that type, and the system is smart enough to give me the normal NSFilenamePboardType's which represent files ending with .png?

