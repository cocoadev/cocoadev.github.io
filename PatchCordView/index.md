---
layout: page
title: PatchCordView
---

How do you make a patch cord interface? Something like Quartz Composer / MAX MSP / PD

----

see FlowChartView 

(please search the wiki before asking questions about things that already exist.)

----

Probably the same way you make any other view. :-) Take a look at the Sketch example in your Examples folder. That will get you started with creating a single view to hold (draw) many elements (shapes in that case). From there, just add your custom logic to draw lines from connector "port" of element A to connector "port" of element B. For fanciness, calculate a slight "dip" in the path to simulate the "gravity hang" of the "cable".

----
And of course, BreakItDown. There are a lot of smaller components that have to come together to make such a thing, and the task is much easier if you worry about each component separately.

