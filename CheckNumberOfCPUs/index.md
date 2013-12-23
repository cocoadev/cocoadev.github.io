---
layout: page
title: CheckNumberOfCPUs
---

Does anybody know how to check the number of processors the computer is running from Cocoa?  I thought maybe NSWorkspace might provide some handy call, but nothing.  This is imporant if you want to spawn a separate thread for each processor you have to get some extra performance, but don't want to spawn threads if you don't have the processors to support them, in which case cycling through threads will just slow you down.  Thanks!

----

On 10.5 Leopard, use:
    
[[NSProcessInfo processInfo] activeProcessorCount]


The info below still works on Leopard, and is helpful if you have to support Tiger or earlier.

----

I don't think there's a Cocoa function for this.  However, you can find this using the BSD sysctl() call.  -- Bo
    
#include <sys/sysctl.h>

int numberOfProcessors()
{
	int ncpu;
	int mib[2];
	mib[0] = CTL_HW;
	mib[1] = HW_NCPU;
	size_t len = sizeof(int);
	sysctl(mib, 2, &ncpu, &len, NULL, 0);
	return ncpu;
}


----

The Carbon function MPProcessors() is also available in 32-bit processes.

