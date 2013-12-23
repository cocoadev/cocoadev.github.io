---
layout: page
title: ShadingsInCoreGraphics
---



Hi, everyone,
I know it's not completely Cocoa, but at least it's related to Apple's APIs.
However, in CoreGraphics, we have the possibility to draw shadings using a CallBack function to which Quartz gives input values and receives output values that supposed to give the color component values.
To create a CGFunctionRef, we need to specify input ranges and output ranges, for output ranges it's easy you have to give as many peer of values as there are components in the color space. However my question is about the input ranges. The Apple documentation says that one can specify an unlimited number of input value ranges... But what's the use of the other ranges ? Only the first input value changes the others don't.
So my question is : what's the use of those other input values ? And another question is : I want to color the shading depending on the X and Y coordinates of the input point and not only depending on the X coordinate, is that possible? If yes, how ?

Thank you very much.

PsychoH13

----

With shadings, the input parameter is limited to a single value (0..1). This is due to the design of the shader, not of the generic callback function creator methods. From the Quartz 2D Programming Guide, under 'How Shadings Work': "The number of input values to your callback. Shading requires that your callback takes one input value".

Given that this value is passed to you, how would it know that you wanted x and y? What you need to do here is to accept the single 0..1 interval and use that to calculate the x and y coordinates that it maps to. You can do this knowing the starting and ending points of the shader that you specified when you called the function in the first place. Note that the value does NOT represent "x" anyway, it represents the relative distance between the start and end points you specified, or looked at another way, how far through your contributing colours it has progressed. Because of this, some effects may not be possible using the standard shaders - for example a gradient that changes in 2 axes cannot be drawn using Quartz shaders - you have to handle that differently. --GrahamCox 

----

Well, that what I meant by "X coordinate", however, I'd like to know if we can draw such shadings or whatever depending on X and Y coordinates because I'd like Quartz to take care of "coloring pixels" precisely. Let me explain :
I want to reproduce with CG or Cocoa or any Apple's API offered with Xcode, a Fractal as someone did with SDL : http://www.siteduzero.com/forum-83-135972-ensemble-de-julia.html , the fractal changes depending on cursor's position. I tried doing that by drawing squares of size (1,1) however this technic is really really slow. I tried drawing a shading, it works greatly and it's very fluid, but... it's on one dimension. 

So what I'd like is to benefit of the floating point precision of CG to draw those kind of things and keep fluidity of the SDL example.
What I found great with shadings is precisely the fact that Quartz give the function a "coordinate" (distance from the starting point) and we could set the color of the line precisely depending on the coordinate. So Quartz is managing everything very fast, that's what I want, but it only works on one axis...
So, is there another way to do the same without loosing fluidity ?

PsychoH13

----
Check out CoreImage. By writing a custom kernel you can perform arbitrary actions based on your coordinates. As a bonus, if the computer has a half-decent video card, your kernel will be executed on the GPU and run unbelievably fast.

----
I have a MacBook... GMA isn't it a quarter-decent fake-video card ?

----
All Intel Macs will run CoreImage on the GPU, but the GMA's speed is much more believable than what you'd get on a better card.

In any case, it will work even with unsupported GPUs, it just runs on the CPU instead. This is slower but CoreImage is still pretty fast even then.

----
Okay, I looked at the kernel, but the language is really restrictive, I can't use for loops or && operators, things that are kinda... important for the function I want to build. So I don't no :( . Another idea ? Or any advises to develop such kernels ?

----
It just takes a certain amount of cleverness to accomplish these things. The language is extremely similar to the GLSL language which is commonly used for all sorts of GPU programming tasks. Loops can be done with a short fixed length. Long loops can be done by running the kernel once, then having some wrapper code that takes the output and feeds it back in to the kernel. You mentioned fractals, which are entirely doable in this manner.

Check out Mandelbrot implemented as a QuartzComposer document using a CoreImage kernel here: http://www.mikeash.com/tmp/Mandelbrot.qtz . And for ideas on how to do this sort of thing, check out the source code for GPULife here: http://www.mikeash.com/?page=software/gpulife/index.html . It doesn't use CoreImage, but it illustrates the sort of thinking and techniques you have to use. Aside from those, there is tons of literature on the net for how to use GPU shaders (which is what CoreImage kernels are) to do arbitrary programming tasks; search Google for "gpgpu" and look at all the hits come in.

----
I'm sorry but I'm new to that language so I don't know how to do such thing, even though I search for tutorials or ideas on the web... nothing conclusive.

