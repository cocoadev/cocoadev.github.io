---
layout: page
title: DeadLock
---

DeadLock occurs when two (or more) threads each block waiting for a condition to become true, unaware that said conditions cannot become true while the other threads are blocked. Imagine two cars on either side of a road trying to turn into a sidestreet on their left (or right, for those of us in England); neither can turn while the other is there, so both sit there indefinitely.

DeadLock can occur when two threads each try to grab a sequence of locks in a different order; thread A tries to grab lock L then lock S, while thread B tries S then L. If both manage to grab the first lock they want, neither will grab the second, and they will sit staring at each other for ever.

DeadLock cannot be resolved at runtime; instead, the programmer must make sure they never set up the potential themselves.

DeadLock has an evil twin, LiveLock.

