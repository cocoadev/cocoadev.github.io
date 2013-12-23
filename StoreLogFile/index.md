---
layout: page
title: StoreLogFile
---



My app is generating a log file, what would be the best place to store my log file?  Is there a folder just for all apps' log files?

* Must it be persistant? If no, use General/NSTemporaryDirectory(). *

*~/Library/Logs is the appropriate place. That's where General/CrashReporter stores logs, etc.*

Make a subdirectory for your app in ~/Library/Logs/. That way if you ever want to put something else in there (more than 1 log file, images, or whatever) you can just put it in there and not have to worry about two possible locations where your log file can be.
