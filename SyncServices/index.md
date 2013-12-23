---
layout: page
title: SyncServices
---



Apple's iSync framework.

iSync uses plugin bundles (suffix .conduit) stored at /System/Library/General/SyncServices. These apparently define a subclass of General/NSSyncConduit which is part of the private framework General/SyncConduit (/System/Library/General/PrivateFrameworks)

[Topic]

----

The Sync Services API will be public in Tiger:

(The following extracts taken from Apple's WWDC session descriptions, which seem to have disappeared but were publicly available at http://developer.apple.com/wwdc/descriptions/ )

427 **Synchronizing Your Data with Sync Services**

This session builds on the Fundamentals of Data Synchronization session and will explain, using example code and demos, how to incorporate Sync Services in your application.

432 **Fundamentals of Data Synchronization**

Learn about the new data synchronization services in Mac OS X. Sync Services make it easy to synchronize your application's data between computers, and with other applications. This session will introduce the fundamental concepts of synchronization and introduce the Sync Services architecture and API set so you can begin to incorporate synchronization into your applications.
