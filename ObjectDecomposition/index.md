---
layout: page
title: ObjectDecomposition
---

ObjectDecomposition is used to break a problem into its components and relationships. Solving problems using ObjectDecomposition is a fundamental piece of the ObjectOrientedProgramming and ObjectOrientedDesign paradigms.

An example -- given the following algorithm:
    
         FOREVER
         { 
           Start Heater.
           While (temp < thermostat + epsilon)
              Do Nothing.
           Stop Heater.
           While (temp > (thermostat - epsilon)
              Do Nothing.
         }

And the following requirements:
I would like to use this algorithm to control a furnace for a home, and also to control the temperature in a reaction vessel at a refinery. I have no idea what the interfaces for these various systems are, but I still want to use the above algorithm. Moreover I don't want this algorithm copied into each system. Instead I want a single binary module that both systems use. It should not be necessary to recompile the algorithm in order to integrate it with either system.

Produce an object-oriented solution.

By utilizing object decomposition you might arrive at the following design:

A Heater_Control object with a run() method that performs the algorithm and abstract methods for start_heater(), stop_heater(), get_temperature(), get_thermostat(), and get_epsilon(). Individual heaters could then utilize the Heater_Control object by sub-classing.

Another approach might be to utilize events and messages, interfaces or protocols, or other mechanisms to describe a heater and data acquisition.

Question: Is this the same as ObjectOrientedAnalysis?

*It sure isn't ObjectDecomposition anyway, since the described system is more or less procedural. The example is a bad one, unfortunately, it's an algorithm, and it's too simple. The Heater_Control object is essentialy just a global scope with the procedures run, start_heater, and so on. To have an example of a object oriented system, there would have to be some more entities that can interrelate. Having an object doesn't make something object oriented.*

*One good way of decomposing a problem description into classes is to use all the nouns from the description, the majority will be good candidates for becoming classes in the the system. For example: "The spaceship must have engines capable of light speed". Possible classes: "Spaceship" and "Engine", and we can also put a relation between them, the spaceship **has an** engine. We've also got "Speed", which knows about the direction and velocity, and possibly a subclass of Speed called "Light<nowiki/>Speed", a relation called **is a**.*

-- TheoHultberg/Iconara

----

I think OOA is more when you look at object hierarchies, cooperation between objects and responsibilities. E.g. if we wish to create a ray tracer, then we probably need at least these objects:

*Figure -- with subclasses like Sphere, Box, Plane, BezierSomething, ...
*Scene -- knows the 3D placement of all figures in this scene
*Camera -- the angle from which the scene should be seen/rendered
*MathUtility -- general methods to calculate dot product, line intersections etc.
*...


And we go on to say e.g. a scene should have a container of figures, thus a scene knows about figure, but a figure does not know about the scene. A camera should have one scene -- the camera should initiate the rendering and ask the scene for e.g. 3D pixels and do the 2D projection etc. etc.

It might not turn out to be a good extensible ray tracer, but I think the above is what one generally classify as OOA.

