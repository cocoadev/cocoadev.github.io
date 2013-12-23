---
layout: page
title: SuddenPerformanceLoss
---



I just installed 10.3.3 on a test machine and found that my program suddenly performs about 50% slower ( on average ) than before. My program's a huge ornery mass of General/OpenGL and physics, 99% C++, and has up until now performed excellently.

Of course, I should run a profiler or Shark and see where the damage is, but I'm curious if anybody here might have seen similar trouble and perhaps might have some suggestions. Superficially, ( I haven't done extensive testing ) it seems to be trouble with the physics, and not General/OpenGL, so perhaps some math libraries were... made slower?

Anybody have any ideas?

--General/ShamylZakariya

----

Sharking it should give the final answer.  I remember 10.3.3 was delayed due to General/OpenGL issues.  Maybe they dorked something up.

----

OK, I sharked it last night, and while I've never actually used shark before ( I've always used the good old-fashioned gcc profiling command line utils, since I learned them on linux ) I did read the tutorial and my rather quick analysis is that General/OpenGL, or perhaps my particular driver ( Rev A PB 12" ) has been severely dorked up.

The General/OpenGl code in my program accounted for, give or take, 5% of my program's CPU time before. My General/OpenGL code wasn't complicated, and my visuals are relatively simple. The frame rate is locked to 20fps, synchronized with a physics engine, with the idea being that as much CPU time as possible is left for the behavioral AI logic that the simulator hosts. Anyway, shark seems to imply that General/OpenGl has become the bottleneck, now taking up something like 30% or 40% CPU time. That's pretty bad. 

Where my program used to run even complicated simulations and AI logic at 100% performance ( e.g., maintaining target simulation timestep of 0.05 seconds, or 20fps  ) now it chokes on even simple test-case stuff, running at best at 35 to 50%.

If anybody here is familiar with Shark, could you give me a hand? I'd be happy to post screenshots of Shark's output, or some sort of log file. This is a perfectly good opportunity to learn Shark, even if I can't actually fix the problem but instead wait for Apple to un-dork the driver. Which of course may never happen.

UPDATE

In case anybody's curious, I spent about 3 hours sharking last night, and fixed the problem. Interestingly, the culprit probably was bad design on my part and not fault on apple's, though it's odd that previous drivers didn't have a problem. Anyway, I was creating and deleting display lists to cache extruded shadow volumes, rebuilding them when the light source relative to the transformed shadow-casting object changed. Since most objects weren't moving, this meant that their shadow volumes could stay on the card, and not have to be recalculated -- this was a fair performance gain. Moving objects,  however, continuously rebuilt their display lists and this was ultimately the performance hit under 10.3.3. All I did was to make a flag for objects in the sim -- those that are static will still store their shadow volumes in display lists but will never check for changes and will never recalculate them. Those that aren't static ( e.g., active moving objects ) will always draw the shadow volume in immediate mode.

The good news is I productively spent my sharking time nailing hotspots in my silhouette calculation & extrusion loops -- they're quite a bit faster now, and amazingly enough, overall performance is *better* than it was before!

I'm now a 100% Shark convert. I had tentatively touched it before, but now that I've seen how immensely powerful and user-friendly it is, I'll use it to analyze all my tight inner loops.

--General/ShamylZakariya
