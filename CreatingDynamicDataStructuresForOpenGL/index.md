---
layout: page
title: CreatingDynamicDataStructuresForOpenGL
---



Greetings all.  I hope I'm asking this on the right site:

Background:  I am working on a course project for which I am implementing some surface subdivision algorithms (see http://en.wikipedia.org/wiki/Subdivision_surface ).  Up until recently I have been doing my implementations in C++ using fltk ( http://www.fltk.org/ ).  My motivations for attempting to implement my project in Cocoa and Objective-C are mostly centered around the user interface design - I find that Cocoa is more intuitive when it comes to adding features to my model viewer.  So far my experience with Cocoa has been centered around the NeHe tutorials and the 0x0 tutorials ( http://www.zerobyzero.ca/~ktatters/tutorials/ ).  

Data structures:  Ideally, I'd like to load the model information from a file (with a pre-defined format as described in http://www.student.cs.uwaterloo.ca/~cs779/Documentation/s3d.txt ).  Additionally, with each step of surface subdivision new points and polygons are added to the model.  

The catch:  In my old implementations, I would be using C++ STL vectors to grow my data structures holding points, edges, and polygons respectively as needed.  However, Objective-C does not offer such functionality.  My options seem to be either to cook something up using NSArray and NSMutableArray, or to use Objective-C++ and its STL.  As I see it, NSArray is possibly too expensive to be of use to me.  On the other hand, I'm having a tough time converting my applications to Objective-C++ without a complete rewrite.  I've spent some time looking for alternatives but results have been disappointing so far.  Furthermore, there do not seem to be any tutorials available for using OpenGL-friendly and Cocoa-friendly dynamic data structures - all Cocoa OpenGL tutorials I've come upon have the coordinates for the polygons hard-coded, and examples that use dynamic data structures for OpenGL are C++-focused.  

What I am looking for:  As things are, I am looking for dynamic data structures in which I can store polygon information.  Ideally I'd like to be able to access them like n-dimensional arrays in C/C++, but I'm open to alternate methods (I have marginal experience in Squeak/Smalltalk and decent experience in Ruby, if that helps).  The most important part is being able to create and populate the data structures on the fly, as there is no easy way to determine beforehand how many elements I will be needing.  

What I am NOT looking for:  I am not looking for third-party libraries to do the subdivision for me, nor do I really want any hints as to how I should organize my data structures (I see the latter as being part of my project requirements, and having someone else do it for me would be cheating in my mind).  

Thank you for your time....


PS:  I'll keep trying to convert my Objective-C applications to Objective-C++.  If using the latter turns out to be the best solution, then I'd like to apologize for any inconveniences caused by the above questions.

----

Just a general tip: the data format of your model information is part of your "model" in MVC terminology; it should, ideally, be completely independent of any code that actually renders it using OpenGL (the "view.") In other words, the data format is irrelevant to the problem you're facing (if you're coding using the common ObjC paradigms.)

----

Actually, after I wrote the above questions and went to bed the gears started kicking in... I think I'm going to implement classes for the containers I need, and see if the application still has reasonably decent performance.  Things do appear to be more straightforward when I think of the vertices and faces of the polyhedra not as data points, but as objects.  

----
You might find ObjCAndOpenGLForAGameEngine interesting.

---- 
I have written a lot of OpenGL code that is integrated into Cocoa applications.  I usually use NSMutableData to store vertex buffers and other OpenGL friendly data structures.

