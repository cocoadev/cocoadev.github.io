---
layout: page
title: LiveLock
---

LiveLock is when a thread (or several threads) runs through a loop that fails without any "progress in the system as a whole" occurring. These terms are somewhat hazy because progress is itself a hazy concept, but essentially if a system repeats itself to no effect (or *spins*), that's LiveLock.

One example of LiveLock occurs in AtomicThreadSafety if one accessor locks the system then sleeps. Because there is no mechanism in place to block another thread if it tries to access the data, that thread will spin over the same instructions until it is slept automatically - a simple case of LiveLock.

A more complex example of LiveLock occurs when two (or more) threads each run a series of operations repeatedly until a condition becomes true, but in such a way that they cancel the others' work out just before they come to test the condition, forcing them both to restart forever.

Imagine two workers trying to build an arch. First, they look around for a keystone; if one is not there, they will craft one, but otherwise they will put it next to where they are building their wall, and then make up the arch, finishing off by putting the keystone in - UNLESS it has moved in the meantime, in which case they let the archway collapse and start over.

Imagine there is just a single keystone. Worker A takes it next to his wall, and starts building his arch. Along comes Worker B, finds the keystone, and moves it next to *his* wall, and begins on his arch. Worker A, having got his arch all ready, turns around and finds he has no keystone. He lets his nice arch collapse, goes off, and takes the keystone back to his wall and starts building. B, on turning around, finds the keystone gone, lets his wall collapse, and repeats the cycle. Like Sisyphus, they are condemned to repeat this insanity for all eternity.

The potential for LiveLock occurs distressingly often in systems that try to minimise blocking of threads. Unlike its lesser twin, DeadLock, it is usually a transient event, as one of the threads will get switched out and allow the other one to complete its rock-toting. However, it is all the more evil for this as it merely results in spontaneous latency hits and nothing more overt.

The term "LiveLock" does **not** apply to systems where *individual threads* may spin in the fashion described above, but the *system as a whole* makes progress. For example, in the atomic version of ProducersAndConsumerModel, an individual producer thread may have to repeat its insert operation several times before it succeeds, but only because some other thread has successfully inserted or removed some data in the meantime.

