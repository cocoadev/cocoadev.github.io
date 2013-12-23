---
layout: page
title: WhyWontMyProcessEnd
---

**Question: I have a Cocoa application that processes large data files.  When I force-quit my application, it won't end.  Why ?**

**Answer:**
I have encountered unkillable processes on several flavors of Unix including General/MacOS X.  
When a Unix process dies, all file descriptors and heap memory are  
automatically released.  [Beware of POSIX processor sets, message  
queues, shared memory, semaphores, and other resources that are not  
released automatically when a process dies or is killed.]  On many  
Unix systems, any data buffered for a file descriptor is flushed when  
the file descriptor is closed/released.  This includes file  
descriptors held open by mmap() and friends.  This policy avoids  
inadvertent data loss.

The most common situation I see with unkillable processes is that  
some pending network IO such as a flush over NFS remains uncompleted  
and the process refuses to truly die until it completes. This is  
particularly problematic if the network connection used by NFS is down.

In the past, I have mitigated this problem by using unbuffered IO  
when possible even though that greatly degrades IO throughput. You  
can try explicitly closing all open file descriptors when the process  
receives a signal, but in my experience that just moves the same  
problem to a different location.  Closing still hangs nearly forever,  
but at least it hangs from your code instead of somewhere deep in  
crt0 or the kernel.
