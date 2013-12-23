---
layout: page
title: ThreadSleepIntervalResolutionTooLarge
---

The maximum precision for thread sleep intervals created by sleepUntilDate is about 0.0025 sec.
I suppose that reducing that interval starts to interfere with updating the UI while the thread is running.

I am putting some complicated drawing code into a separate thread so it doesn't freeze the interface while
it is executing. The number of plot elements can be in the tens of thousands, so in my code, and on my
hardware that drawing can take on the order of a minute or two to execute with a thread timer.

Unfortunately, the drawing cannot be split into multiple threads, because the later part of the drawing is
not predictable from the early part (it is a kind of fractal, but not of the Mandelbrot set type - it is a
chaotic time series in 2D.)

Do I have any alternatives to this kind of single-threaded drawing?

----

Maybe you can draw to an offscreen view in the separate thread. This might not interfere with the UI and you might be able to display it much quicker all at once. But OS X is double-buffered, so it seems to me it would already do this automatically�
----
An offscreen image is definitely the way to go here. In addition, this will give you direct access to the image buffer and will yield **FAR** better performance when manipulating individual pixels (assuming your algorithm does) than fooling around trying to draw 'little  dots' with Quartz.

----
**
OP again: Unfortunately, what I most want to see is the developing image, not just the finished one. Apologies for not making this clearer in the original question.
It's like a trajectory. I want to see where it's meandering while it's wandering around, kind of like an animation of a random walk.
In other words, the time evolution is not obvious from the final plot of the entire series (which is a 2D time series of individual states plotted in a plane)

This is a tough one, and may be a more sophisticated problem than my level of expertise can handle. The real-time display is important because the
trajectory itself has fractal properties - it sometimes just looks like an unraveled ball of yarn when the time series is finished.
The part that computes each General/NSPoint in the time series is an ODE solver that spits out results pretty fast on the command line, so it's not hung up there.
My drawing code actually draws a tiny General/NSRect at each of those points, but the resolution is such that for most of the trajectory the sequence of
General/NSRect looks like a continuous line until about three or four steps of the solver are calculated for every state that is plotted.**

----

No problem. Draw the offscreen image, but have the main thread periodically show it while in progress. It's like gaming; you've got a physics timer and a separate animation timer. Your physics timer is however fast you want the fractal to grow, but your animation timer is only as high as the UI can handle. Your eye can only see maybe a hundred frames per second anyway.
----
Actually ~30FPS should be sufficient for a visually pleasing user experience. Keeping the frame rate down will also allow for a responsive system experience as well. ;-)

----

I've seen the consequences on my trusty old hardware. Sleep intervals for the so-called "physics" thread that are less than about 0.008 sec play havoc
with the performance of the interface. Consider this, then: plot 5000 states at 125 per second = 40 seconds.
Further consider that sometimes I might want 25000 states.

The sleep-timing helps because (am I right??) the behavior should stay the same across faster and slower hardware.

At the drawing scales I am using, drawing 500 (five hundred) states generates a line that, if you straightened it out, would be on the order of 10 cm in length.
So if I clump the output to come out every 0.03 seconds, each chunk of the path would come out about 1 mm at a time. Math look OK?

That looks like it might work for the display. The finished drawing (as I said) is
kind of like a ball of yarn projected into 2D, like a random walk inside a 6' x 6' cell

---- Sounds like a chaotic orbit rather than a fractal one...
---- Hence the OP saying "it is a chaotic time series in 2D"
---- Oh, yes, missed that! Thx.
