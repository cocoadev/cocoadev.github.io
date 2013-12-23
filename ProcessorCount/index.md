---
layout: page
title: ProcessorCount
---

The processor count can be obtained via the following function:

    
#import <sys/param.h >
#import <sys/sysctl.h>

int getProcessorCount() {

  int     count ;
  size_t  size=sizeof(count) ;

  if (sysctlbyname("hw.ncpu",&count,&size,NULL,0)) return 1;

  return count; }


This code was copied from the web page:  http://alienryderflex.com/processor_count.html

----
It is much shorter, however, to simply call     MPProcessors().

----

Isn't that a Carbon function?  Is there a pure Cocoa equivalent?

----
Who cares what framework it comes from? It works! It's not as if     sysctlbyname() is "pure Cocoa" either. This hardheaded insistence on using "pure Cocoa" solutions when very obvious and simple Carbon functionality is staring you right in the face is something I've never been able to understand. You invoke a function with no parameters and it gives you back an int, that's all there is to it. The fact that it's "Carbon" doesn't reduce the elegance or make your life more difficult in any way.


----

Sometimes I think the people who ask for a Cocoa equivalent for something like that don't really know what Cocoa and Carbon are.....(i.e. big C libraries).

----
There is also a geek-way to do it, I use it in m<nowiki/>iniStat2 widget.

    
#import <mach/mach_host.h>
#import <mach/vm_map.h>
#import <mach/machine.h>

int processorCount() {
	kern_return_t kr;
	mach_msg_type_number_t ncpu, count;
	processor_info_array_t info;
	
	kr = host_processor_info(mach_host_self(), PROCESSOR_BASIC_INFO, &ncpu, &info, &count);
	if (kr == KERN_SUCCESS) {
		vm_deallocate(mach_task_self(), (vm_address_t) info, count * sizeof(int));
		return (int)ncpu;
	}
       return 0;
}


Actually, applications should not use this information to decide about going to multi-threaded or not. They should always do so :)

-- DenisGryzlov

----

Re my Carbon/Cocoa question:  It was just a question; no need to jump down my throat.  So there won't be any issue about MPProcessors becoming deprecated or anything like that?  I'm not worried about whether it works now, but whether my projects can be expected to compile without glitches in the future.

DenisGryzlov, I agree with you that apps should be multithreaded even on a single-core machine.  But how many threads to create?  If there are 4 processor cores, then I will need to create four background-processing threads to take full advantage of the power of the machine.  If there are 8 cores, I need to create 8 threads.

----
You're still displaying strangeness with regards to the whole Cocoa/Carbon thing though. Your deprecation question shows it. Cocoa things can and have become deprecated as well. MPProcessors is not *currently* deprecated, and that's all I can tell you.

As far as being multithreaded, there's no intrinsic virtue in it. If multithreading can make your application more responsive on a single-CPU machine then that's great, but if it doesn't then you may as well just run one thread on them. Dynamically spawning different numbers of threads depending on how many CPUs a machine has is a good idea, in that it keeps your application performing well even on unanticipated hardware, and it improves efficiency (although probably not noticeably) on machines with fewer CPUs.

----
I don't think Carbon functions would be deprecated anytime soon. They are not only for Classic compatibility, but they are also may be used when porting apps from procedural languages from Windows and other platforms. Any function in Cocoa or Carbon could be deprecated, though. This can't brake your app, because you will have at least 2-3 years to *repair* your code with deprecated functions. I bet that     MPProcessors function will be available even if Apple decides to move from UNIX platform in MacOSX, while *sysctl* - will not :) --DenisGryzlov

----

Cocoa-ish way:

NSUInteger a = [[NSProcessInfo processInfo] processorCount];
NSUInteger b = [[NSProcessInfo processInfo] activeProcessorCount];

