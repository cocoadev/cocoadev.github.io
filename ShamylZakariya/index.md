---
layout: page
title: ShamylZakariya
---



I'm a graphic designer (for an advertising & PR agency -- "which one?" : "A major one") by day, and a monomaniacal programmer by night. I'm new to cocoa, having only come to mac (as a dev platform) in february 2003. Previously I developed in c/c++ on linux and before that BeOS. On the other hand, I've used macs since about 1990 for graphic design; and I learned c and pascal on a mac classic way back when in high school. You know, back when the machines had so little memory you had to do dirty tricks like using the display as a memory buffer. I've also held a job writing device diagnostics and interface code in c and assembler for digital set-top boxes.

My work is purely hobby and is mainly ( 99% ) robotics and Ai related. Nonetheless, I write a little this and that as side projects to stay sane.

Check it out -- recorded directly from opengl to quicktime:

http://home.earthlink.net/~zakariya/files/Clumsy_2.mov

And screenshots:
http://home.earthlink.net/~zakariya/files/Wandering.png
http://home.earthlink.net/~zakariya/files/RangeFinder.png
http://home.earthlink.net/~zakariya/files/Inchworm.png
http://home.earthlink.net/~zakariya/files/TooComplex3.png

( and a mahjongg game )
http://home.earthlink.net/~zakariya/files/Mahjong/BlowMeDown.png

- Your link is broken! (fixed, sorry) (it's still broken...)
Yeah, well, I ran out of space. I had to kill it!

- Did you use stencil shadows or what for the ray .pngs? Those images are really nice. Would love to see the movie.

Sorry about the bad links, I have only 10 megs on earthlink, and since the consensus around here is to punish people who update their personal pages too often I left it alone.

To answer your question: yes, I used stencil shadows, and my own take on toon-style rendering, with a focus on sharp color and simple outlining -- perfect for technical 3d. I also use multisampling, since it's not a game, so FPS is less important than precision. This all runs happily at ~30fps on my little GenA PB12"

----
Sorry for cluttering your page -- delete after reading.  I'm a PhD student.  At what universtiy?  "A major one".  I am very interested in your virtual world work and given some more information about how you get objects into the world and given some specs on your physics model I might like to ask if you would be interesting in contributing/collaborating on an AI/AL project.  Let me know if you might be even remotely interested.

---- 

I'd be happy to collaborate, but I have to be upfront and say that my work is mainly to get my foot in the door to get *into* a graduate program in AI. Having been an undergrad art major, and working right now as a graphic designer, I need to be able to demonstrate my ability... if I want schools to take me seriously.

So anyway, my simulator is just a modular backend for a general purpose control API, for which I also have written/built a hardware backend. I'm building a robot in my free time which I hope to make work in the real world, using the same "brain" as I've developed in my simulator. So, I don't intend to modify my main program unless it's for the better.

On the other hand, my simulator itself is pretty general purpose, loading simulations from XML-like files ( I designed my own format and wrote my own parser since I want to be able to run the brain from lightweight hardware, like a Sharp Zaurus ) and handing off the physics to ODE and handing off representation and semantics to my own code. I've written a fairly robust C++ object model to ride on top of ODE's C interface, and a corresponding C++ graphics API which is completely independent except for some code to handle ODE's matrix tranformations.

I don't know how familiar you are with the open dynamics engine, but here's what I support ( or have independently derived ):


*Hinges -- with optional spring-return to center
*Sliders -- again with optional spring return to center
*Rotary motors -- unbounded rotation
*Rotary servo motors -- range-bounded positional
*Linear piston motors -- range-bounded positional
*Range-finders -- distance measurement
*Heat sensors
*Compasses
*Solid objects -- spheres, cylinders, boxes
*Mesh terrain -- loaded from a heightmap image.
*Atmospheric drag -- primitively implemented, but good enough.
*Antigravity -- objects can choose to disregard gravity
*Flexible friction model -- each object can define its own friction
*Attachments -- arbitrary stiff attachments between objects


What I don't support ( yet )

*Ball joints -- never needed, so never implemented
*Universal Joints -- never needed, so never implemented
*Geom Transform -- needed, but I couldn't get it to work. So I implemented Attachment
*Linear unbounded motor -- think "rocket engine"


Anyway, if you want to talk with me about this stuff, email me at shamyl@zakariya.net

I'm willing to work with people. I've always intended to release this as open source at some point, but I've wanted to keep this to myself until I reach a 1.0 release or get into a university ;) I've had bad luck with open sourcing my work, where people took off with my code and then bickered about theming and website design until the project died -- so I'm a little sensitive. In case you're wondering, see http://slicker.sourceforge.net/ -- I wrote the core libs, gui, plugin model and all on my own back when I was on linux, but as soon as I made the source public it died, horribly.

--ShamylZakariya

Have you heard of Irrlicht Engine? (http://irrlicht.sourceforge.net/) The author open-sourced it but has kept very strict control. So basically people can use the source but all patches go through him. He still controls the "official" version. You might consider something like that eventually, assuming you trust people not to break the license agreement. Unfortunately it's C++, like almost everything else out there. I'd love to look at your code some day, especially for the scene graph but just in general. � BrentGulanowski

