---
layout: page
title: KillProcessAndChildren
---

This is more of a unix question, but anyway...

From a Cocoa app I am launching a process using     fork()/    system() (yes, I also tried NSTask, but with the same problem).  The problem is, that the process I launch will also launch a process, and so, when I kill the process I launched, the child of it will stay around.

This was initially why I switched from NSTask to     fork()/    system(), cause then I could use     setpgrp() for the new process and use     killpg() to kill the process group, but it gave the same result (and anyway, the child of the new process had the process group ID to be the process ID of the process, not the group ID.

I also tried     kill(-getpid(), SIGTERM) but w/o luck.  The only thing which works is     kill(0, SIGTERM), but unfortunately that will also kill my own task.

So my question is, how to kill a process with all its children?
    
- (void)awakeFromNib
{
   setpgrp(0, getpid());

   int parentID = getpid();
   if(fork() == 0) // child
   {
      setpgrp(0, parentID);
      system(cmd); // cmd will spawn a new process and wait for ctrl-c
   }
}

- (void)applicationWillTerminate:(NSNotification*)aNotification
{
   kill(0, SIGTERM);
}


----

I looked in the documentation for NSTask.

Is     - (void) terminate  the magic bullet for this one?

