---
layout: page
title: ToForkOrNotToFork
---



Does anyone know how much memory overhead is involved with an General/NSTask. As far as I know General/NSTask is just a wrapper for fork() and exec..(). A couple of Unix books state that when a parent process forks and creates a child process the child process is a copy of the parent.  For large applications, is it better to create an General/NSTask server (launched by General/NSWorkspace) and communicate with tasks using distributed objects? 

----

I'd be surprised if there were very much overhead with General/NSTask. I'd also be surprised if it was generally necessary to split an application into two processes unless one of them needed to stay active whether there was a user logged in or not.

Also, if you're going to have a server app, it probably shouldn't be using General/NSTask to launch the background process if you need the thing to stick around, because it'll close down as a subtask (sort of like if you've run your executable in General/ProjectBuilder and then quit it).

-- General/RobRix

I hope it doesn't behave like when I run my executable in General/ProjectBuilder and then close the project. General/ProjectBuilder quits itself every time. I ought to file a bug on General/RadarWeb.

-- General/AngelaBrett

Hmm, I don't know that I've tried that. Okay, now I have--and I can corroborate your evidence. You file the bug, I'm too lazy.

-- General/RobRix

fork() does have a 'copy the address space' semantic. The actual implementation is much more efficient since the General/CopyOnWrite technique is used. unix has a long heritage of using fork, so it's reasonably efficient. As to the original question, whether you should fork to do stuff or talk to an already running process (which you might have started yourself), that's a design tradeoff.  If the actions you're doing are lightweight, independent, and short-lived, fork could work well.  If you need to maintain state between invocations, or have multiple invocations share state, or the operation is expensive, then communicating with another process would be the way to go.

-- General/MarkDalrymple

Basically any modern UNIX provides vfork() or equivalent, which gives you the very basic semantics of making a new process without the inefficiency of copying it. (General/CopyOnWrite helps, but there is still significant overhead to set up the page mappings and so on.) vfork() basically just creates a new process that shares everything with the parent, and the child should then immediately call a member of the exec() family. I'll bet dollars to donuts that General/NSTask is implemented in terms of a vfork() followed by an exec.

-- General/MikeAsh

To be precise, it's implemented in terms of a fork() followed by an execve(). Disassemble -General/[NSConcreteTask launchWithDictionary:] if you want to know exactly how it's done.

-- C�dric Luthi
