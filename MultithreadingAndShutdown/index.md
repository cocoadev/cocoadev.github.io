---
layout: page
title: MultithreadingAndShutdown
---

I spawn a thread (using NSThread) which is responsible for fork()'ing and monitoring the output from this forked sub-process (using pipe/file descriptors). When the sub-process has output, it is read by my thread and sent to the main loop.

This works fine, but there is one problem -- during shutdown, my forked sub-process will continue to run. I have a mechanism to quit it, but this requires sending a message to the thread (which then signals its sub-process), but if I send a message to my thread when receiving a NSApplicationWillTerminateNotification, my thread is not awakened.

So basically starting and quit'ing my application will leave tasks hanging.

Either I can issue a killpg() in the 'will quit' notification, but that will quit the main process as well (and that is bad, as other components may also wish to perform some actions in 'will quit', like saving user defaults) -- alternatively I may be able to implement the 'should quit' delegate method, and reply with a NSTerminateLater and then reply when all sub-processes have quit, but this is very cumbersome, because the application delegate (which needs to implement this method) doesn't really know about these sub-processors (which are handled in autonomous sub modules of my application).

Is there a graceful solution to this problem?

----

One solution is to have a dummy object registred for the will terminate-notification, and store the process ID in this object, so that it will issue the kill. So the code is something like:
    
+ subthread
{
   if(int pid = fork()) // parent
   {
      MyKillProcess* killObj = [MyKillProcess killProcessWithPID:pid];
      [[NSNotificationCenter defaultCenter] addObserver:killObj
        selector:@selector(applicationWillTerminate:)
        name:NSApplicationWillTerminateNotification object:NSApp];
      ...
      waitpid(pid, &rc, 0);
      [[NSNotificationCenter defaultCenter] removeObserver:killObj
        name:NSApplicationWillTerminateNotification object:NSApp];
   }
   else // child
   {
      ...
   }
}


Should your application be terminated between registering your object and unregistering it, then it will receive the applicationWillTerminate: method, and can kill your child.

In theory though, it should be possible to terminate the program *before* registering the object but *after* your fork().

----

Check out ThreadWorker and see if it doesn't do what you want. It provides a     cancelled boolean value  you can set and test on in your thread.

*Thanks for the pointer, but it doesn't.*

