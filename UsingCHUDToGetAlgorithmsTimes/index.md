---
layout: page
title: UsingCHUDToGetAlgorithmsTimes
---



I need to measure an algorithm's times and I've heard about CHUD. I've taken a look at it, but i don't know which tool i must use nor how do they work. Does anyone know it?

Thanks in advance

----

Hm, I don't know about algorithm's time, but with General/SharkCHUD you can analyze your program and see the percentage of the processing time used by the different classes/functions during the analyze phase. With that you can see which function call used the most processing power and you also get some hints what to optimize. Very nice tool.

--General/ThomasSempf

----

If you just want to find out how long a function takes to complete, you can use     clock() to time it. It's not terribly accurate, so you should run the function in a loop to make sure it takes at least several seconds to finish, then divide by the number of iterations to get a time per call. CHUD is for overall performance evaluation on a more complex level, like finding out not only how much time something takes but where it's spending that time and why. It's overkill for your purpose, I believe. *Most importantly, you won't get a timing, I think?*


----

Thanks. After playing a bit with CHUD that is what i think. I was using     clock() but my project's director told me about using CHUD, but I suppose it's not exactly what he thought.

----

If your algorithms use General/ObjC methods (which they won't if you use legacy code in C), then you could use General/AspectCocoa to make the timings more dynamic (switch them on and off) and flexible (add/remove methods for the timings). --General/CharlesParnot

I don't know if you get ns with     clock(), but I think darwin can get you this kind of precision if you need it. I have seen it on apple's website somewhere. --General/CharlesParnot

----

Saturn (CHUD) is good for timing and profiling code. Also,     gettimeofday (man 2 gettimeofday) will yield microsecond precision (standard disclaimer regarding system clocks and preemptive multitasking variations apply) if you want to time things yourself in code.

----

Wouldn't General/NSLog be a good solution? Just make an General/NSLog entry at the beginning of your algorithm and at the end and employ a little subtraction. Or is this not an Obj-C project?

----

You can also use the CPU's timestamp counters which are incremented every cycle.

    
#define tbl(x) __asm__ __volatile__ ("mftb %0,268\n\t":"=r" (x) )
#define tbu(x) __asm__ __volatile__ ("mftb %0,269\n\t":"=r" (x) )

/** returns an unsigned long long read from the CPU's timestamp counters. */
inline unsigned long long gettimestamp() {
  unsigned long long full;

  register unsigned int upper;
  register unsigned int lower;

  tbl(lower);
  tbu(upper);
  
  full = upper;
  full = full << 32;

  return full+upper;
}


Its not the most beautiful code, and should be compiled -O3 (gcc is better at optimizing than I am). But it does save having to make a system call, and is much lighter weight.
