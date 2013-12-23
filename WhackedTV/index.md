---
layout: page
title: WhackedTV
---

General/WhackedTV is a replacement for the venerable Carbon Hack TV. It shows how to use the Sequence Grabber API's to capture movies from external video and audio sources, just as Hack TV, but adds many important new features, including simultaneous capture from multiple SG Channel, use of the new SG Audio Channel, http://goo.gl/General/OeSCu video preview using ICM Decompression Session and General/OpenGL.

A copy of General/WhackedTV can be downloaded from: http://developer.apple.com/samplecode/General/WhackedTV/index.html

----

I'm having trouble getting decent compression and performance out of General/WhackedTV. On an intel machine, using the default settings, performance is very reasonable (about 10% of CPU on a MPB@2.16GHz). The saved video is compressed as CCIR-601 uyvy, which consumes a lot of space.

If I change the video compression settings, e.g. to Mpeg4 or H.264, I get decent compression, but CPU usage screams, to 60% to 100%. If I run the code on a PPC machine (a dual G5), CPU consumption is 100%, even with default compression (I get similar PPC behaviour with General/MulleSight).

Other video apps, such as General/SecuritySpy,don't seem to suffer from this problem at all, CPU consumption varies a little between PPC/Intel and different compression settings, but by a few percent, not orders or magnitude. Can anyone enlighten me as to what is going on here?

----

Weird. All I can say is that some profiling should be in order. Since the code is available, this should not be problematic. I say again, weird. Computers are weird. --JJ

----

I just played around with Shark. On the General/PowerPC machine, the Heavy (Bottom-Up) view of the PPC process is as follows. This shows the first four entries, with the first two expanded.

    
67.1%	67.1%	libGLImage.dylib	glgProcessColor(General/GLDPixelMode const*, General/GLGOperation const*, unsigned long)
0.0% 	67.1%	libGLImage.dylib	glgProcessPixels
0.0%       67.1%	General/QuartzCore	fe_bitmap_copy	
0.0%	       67.1%	General/QuartzCore	image_buffer_get_data
0.0%	       67.1%	General/QuartzCore	texture_retain	
0.0%	       67.1%	General/QuartzCore	fe_texture_new	
0.0%	       67.1%	General/QuartzCore	fe_tree_create_texture
0.0%	       67.1%	General/QuartzCore	fe_tree_render_apply
0.0%	       67.1%	General/QuartzCore	fe_tree_render
0.0%	       67.1%	General/QuartzCore	fe_tree_render_image_
0.0%	       67.1%	General/QuartzCore	fe_tree_render_image
0.0%	       67.1%	General/QuartzCore	fe_image_render_
0.0%	       67.1%	General/QuartzCore	fe_image_render
0.0%	       67.1%	General/QuartzCore	-General/[CIOpenGLContextImpl renderSoftware:matrix:bounds:]
0.0%	       67.1%	General/QuartzCore	-General/[CIOpenGLContextImpl render:]
0.0%	       67.1%	General/QuartzCore	-General/[CIContext drawImage:inRect:fromRect:]
0.0%	       67.1%	General/WhackedTV	-General/[SampleCIView drawRect:]

19.2%	19.2%	Unknown Library	0x58b9000 [3.5KB]
0.0%	       19.2%	General/QuartzCore	fe_span_iterate
0.0%	       19.2%	General/QuartzCore	jit_program_renderer
0.0%	         9.8%	General/QuartzCore	fe_fragment_thread
0.0%	         9.8%	libSystem.B.dylib	_pthread_body
0.0%	         9.5%	General/QuartzCore	fe_fragment_render_quad
0.0%	         9.5%	General/QuartzCore	fe_tree_render_apply_1
0.0%         9.5%	General/QuartzCore	fe_tree_render_apply
0.0%	         9.5%	General/QuartzCore	fe_tree_render
0.0%      	  9.5%	General/QuartzCore	fe_tree_render_image_
0.0%          9.5%	General/QuartzCore	fe_tree_render_image
0.0%	          9.5%	General/QuartzCore	fe_image_render_
0.0%	          9.5%	General/QuartzCore	fe_image_render
0.0%	          9.5%	General/QuartzCore	-General/[CIOpenGLContextImpl renderSoftware:matrix:bounds:]
0.0%	          9.5%	General/QuartzCore	-General/[CIOpenGLContextImpl render:]
0.0%	          9.5%	General/QuartzCore	-General/[CIContext drawImage:inRect:fromRect:]
0.0%	          9.5%	General/WhackedTV	-General/[SampleCIView drawRect:]

3.3%	          3.3%	General/QuickTimeComponents	YUV422_UC_To2VUY_W1x
1.8%	          1.8%	General/QuickTimeIIDCDigitizer	General/APWVDOIIDCDigitizerEntry


On the Intel machine, the top four entries, unexpanded, are as follows:

    
31.2%	31.2%	General/QuickTimeComponents	YUV422_UC_To2VUY_W1x
8.5%	        8.5%	mach_kernel	ml_set_interrupts_enabled
8.0%	       8.0% 	ATIRadeonX1000GLDriver	gldCreateQuery
6.3%  	6.3%	       commpage [libSystem.B.dylib]	__memcpy


To a first approximation, it looks like the first two stack traces on the PPC machine account for most of the extra CPU consumption. 

I did wonder if it might be something to do with the video hardware. Both of these profiles were taken with an External iSight connected to a built-in General/FireWire port, and a Universal build of General/WhackedTV. On the Intel machine, the video card is an ATY,RadeonX1600, with 256MB of RAM. The display is the built-in MBP display. On the PPC machine, the card is a General/GEForce FX 5200, with only 64MB of RAM, and its running a pair of external monitors, at 1680 x 1050 and 1600 x 1200. The fact that other camera apps seem to run OK seems to suggest that this isn't really a hardware issue.

The calls to     -General/[CIOpenGLContextImpl renderSoftware:matrix:bounds:] looks suspiciously like a software renderer is being used for some reason on the PPC machine, which might be closer to the root cause. My question then would be, how do I stop this happening, as other apps seem to be able to on the same hardware?

----

Update - it looks like the problem is connected to the General/GeForce 5200 - See this technote for details - http://developer.apple.com/qa/qa2005/qa1416.html

Thanks to David Duncan on the quartz-dev list, and Brad Ford on the quicktime-api list for pointing me in the right direction. I'm just about to try the suggested workaround now.

----

Yup. That was it. With the General/GeForce 5200, which both my PPC test machines had, the CPU is used for General/CoreImage rendering by default. Passing 

    General/NSDictionary *contextOptions = General/[NSDictionary dictionaryWithObjectsAndKeys:General/[NSNumber numberWithBool: NO], kCIContextUseSoftwareRenderer, nil];

into the General/CIContext factory method forces GPU rendering, and reduces video preview only CPU consumption to 45% on a powerbook G4 12" and 20% on the dual G5, which is a lot more acceptable.
