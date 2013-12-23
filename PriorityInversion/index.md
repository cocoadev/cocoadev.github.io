---
layout: page
title: PriorityInversion
---

PriorityInversion occurs when a high priority thread has to wait around for a low priority thread to finish what it is doing before it can continue itself. Thus the perceived priority of the thread is temporarily inverted.

This term is most commonly used in reference to locks, when a low-priority thread holds a lock and thus blocks out a high-priority one. This situation is actually the most easily fixed - just temporarily raise the priority of the thread holding the lock.

The scenario is more general, however, latent in every ProducersAndConsumerModel, and indeed in any setup where one thread waits for another thread to signal a condition is true (see NSConditionLock). Such cases are much harder to fix automatically, as it is usually not apparent which thread is waiting for which to complete. Be wary of any claims otherwise.

This issue is not very important to most Cocoa programmers, as we generally do not worry much about thread priorities anyway.

