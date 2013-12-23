---
layout: page
title: SuspendingNSThreads
---



I'm writing a program that has movie video playback which cannot, under any circumstances, drop a frame during playback. If it does drop frames, it stops playback and posts an alert to that effect.

The problem is that I want to do some background processing when playback isn't happening. What I want to do is spin off some threads to do the processing I need (to keep the UI responsive), but pause them if/when playback starts. And the user shouldn't have to wait a few seconds to start playback; it should start quickly.

General/NSThread, of course, has no pause method. pthread.h isn't helping me much. Would calling setThreadPriority: with an extremely low priority work? Does anyone have any suggestions?

----

First, a disclaimer: *Multi-threaded programming is an insidious nightmare and should be avoided whenever possible.*

A common solution is to use a semaphore and let the worker threads block on the semaphore.  For example, have each worker thread periodically sem_wait and then immediately sem_post.  When the "master" thread  wants to suspend all worker threads, have it sem_wait, then do its high priority work and only sem_post when it is done.  The worker threads will all soon block on the semaphore and not wake up until the "master" thread calls sem_post.   By the way, if you were not deliberately trying to make the "master" thread the highest priority thread, this technique is a bug called a "priority inversion"... one of the many *insidious nightmares*.

NAME
     sem_wait, sem_trywait -- lock a semaphore



SYNOPSIS
     #include <semaphore.h>

     int
     sem_wait(sem_t *sem);

     int
     sem_trywait(sem_t *sem);



DESCRIPTION
     The semaphore referenced by sem is locked.  When calling sem_wait(), if
     the semaphore's value is zero, the calling thread will block until the
     lock is aquired or until the call is interrupted by a signal. Alterna-
     tively, the sem_trywait() function will fail if the semaphore is already
     locked, rather than blocking on the semaphore.

     If successful (the lock was aquired), sem_wait() and sem_trywait() will
     return 0.  Otherwise, -1 is returned and errno is set, and the state of
     the semaphore is unchanged.

----

Believe me, I have seen the nightmares, even the ones that are less insidious (more sidious?). I would love to be able to use something simpler, but such is not my lot.

So, if I understand correctly, every few lines of code (depending on how long it takes to execute), I need a sem_wait/sem_post pair. Then, when I start my playback, I'll call sem_wait, which won't return until all the other threads that use it are suspended. If I deliberately make the playback thread very high priority and all the worker threads very low priority, I shouldn't have a problem, provided I call sem_wait/sem_post often enough in the worker threads.

----

Hey,
If video playback is anything like audio playback (and I suspect it is), your playback thread can never block for an indeterminate amount of time. This means no malloc/free's and NEVER call a vanilla semaphore wait function in the video thread. If you must wait in the video thread, use a timed semaphore wait. (This all assumes that somewhere in your code you are interacting with a realtime audio/video subsystem, if you are writing your own av system, then have fun, all bets are off!)

Unfortunately, by the way I understood your post, you need the worker threads to have priority over the render thread at some points. In this case, I would not try and take literal priority over the render thread, but only the resources it needs. For example, rather than having the render thread wait on a semaphore, have it poll a FIFO for the data or messages it needs to proceed. Then all you have to worry about is the worker threads, as long as at some point the worker threads sends the data to the render thread, you know the needed work will be done. Try to use semaphores for render thread->worker thread communications only, for example reminding the worker that more data will be needed shortly. Use non-blocking structures like FIFO's or ring buffers to communicate worker thread->render thread. Oh yeah, last time I checked, mach semaphores are the only semaphores on OS X to have a guaranteed non-blocking signal function.

The bottom line is, never depend on the state of your real-time (or very high priority) thread. This means that your worker threads should never know or care about whether the render thread is sleeping or awake, they should just do their jobs. For more info on the vary painful subject of realtime media streaming, download the General/CoreAudio SDK, and take a look at the C++ class library included. It is filled with examples of lower-priority worker threads interacting with the realtime audio thread. Just be warned, this is a very complex subject, filled with sometimes counter-intuitive techniques.
--General/JeremyJurksztowicz

----

It's the opposite, actually -- the playback thread should NEVER block, and all non-essential-to-playback threads should just stop in their tracks and wait for playback to finish. Nothing, but nothing, is more important to my application than never, ever, dropping a single frame of playback.
Thanks for the General/CoreAudio SDK idea. I'm downloading it now.

----
I thought that's what I said? Me no parle anglais so good. Also, If you want some more source code, e-mail me (jurksztowicz AT gmail DOT com). I have been teaching myself realtime audio programming for ..er 3 years (already?!). Not a lot of time in such a complex field, but I have managed to write a zero latency added sound engine. It uses the semaphore/ringbuffer/FIFO techniques, but manages to wrap them up in a General/SoundNode base class, which insulates almost ALL realtime management from clients and subclassers. My goal was to make a sound engine that a novice programmer could use and extend. Anyways, my self promo is over, good luck and don't get discouraged, this subject is full of traps! -- General/JeremyJurksztowicz
