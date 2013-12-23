---
layout: page
title: DoesAProcessGroupCountAsASingleProcess
---

see HowToKillAFrozenThread for the discussion that motivates this discussion

I have decided to use subprocesses to perform tasks for a server. I was wondering if using a process group will count as a single process toward the process table. I'm would like to keep the amount of overhead each subprocess creates down to a minimum. 


----
A "subprocess" is indeed a unique process.  There will be significant overhead for "forking" numerous processes because each process must create its own memory space and then copy over the contents of the parent process into this memory space.  The only advantage (that I know of) of using a process group is the ability to kill an entire process group, rather than just one process.

In general, threads are much faster than creating new processes.  But having a new memory space can be advantageous.  I believe in the default configuration, apache runs multiple (2 or 3) processes as well as several worker threads within each process if there is enough demand.

-JoeCrow

----

The "significant overhead" will be avoided because each subprocess will dynamically load it's code (using bundles) after the process has forked, and nothing from the main process will be modified (that is, no copy on write should take place). Obtaining a new memory space is why I am using tasks instead of threads (see HowToKillAFrozenThread).  

I was just wondering if it was worth my time to define a process group for all of the subprocesses. My hopes are that a process group only takes up one space in the system's process table. Does anyone know how many processes a single user is allowed to run in OS X?

*    ulimit -a lists 256 as the maximum number of processes on my system.*

