---
layout: page
title: StackFramesWhenIsTooMuch
---

If you pile your stack frames a mile high you will get a crash -- but it has to be an INSANELY large amount of frames; eg a recursive call gone bonkers.  How can I tell how many frames my system will permit me?

----

Not really a count, but a size. There is a bunch of information on this in the docs access by (Help/Show Documentation Window) from Xcode. A relevant function that I found using the documentation that may be of interest to you would be StackSpace. I would also listen to Apple's warning regarding this function (and others) stated in the "Optimizing Your Code for Carbon" in the "Carbon Porting Guide"; *You should, however, think about how and why you are using them. You�ll probably want to consider additional code to better tune your performance.*. If you actually *NEED* this function, I would take their advise and redesign.

----
Hell yes, I can hardly imagine a scenario when you would need to go that deep into the stack.  The only time I've ever hit the cieling was in recursive function whose terminating condition was buggy.  But I was curious as to where the limit is, and what exactly determines it.  In college language instructors and even compiler design professors as well as OS teachers never liked talking about the stack.  Figuring out where you were in the stack (in terms of what frames were beneath the top most one) was something that was almost a taboo topic.  I understand why you want to let the stack run its natural course and not screw with it (e.g unwind manualy or even just look at it) but if you're working in C were you havae to deal with the internals of your machine anyway, might as well know something about how to handle the stack.
----
After a little playing around, I found you can also determine the size of your threads stack, it's base address, and where you are in the stack via. the pthreads API.

Take a look at     pthread_get_stacksize_np and     pthread_get_stackaddr_np.     pthread_get_stacksize_np returns the total allowable size of the stack, and     pthread_get_stackaddr_np returns it's base address. You can use pthread_self to get the current threads pthread_t that these functions require.

The less-than-correct (read quick and dirty) way to determine where you are in the stack would to subtract the address of your last local variable from the base address. The correct way (I assume can be done via inline assembler) would be to grab the current value of the stack pointer and use that for the subtraction, although I am not immediately sure how to do that on PPC/OS X.

----

If you were going to do that, the stack pointer on OS X is PPC GPR1.  Source:  http://developer.apple.com/documentation/DeveloperTools/Conceptual/MachORuntime/2rt_powerpc_abi/chapter_9_section_4.html

--JeffDisher

----
Thanks for the info, I was not the original poster, just trying to give some alternatives and more useful info other than the StackSpace function I mentioned. All of my assembler experience is on x86 platforms so that link will serve for some good reading.

