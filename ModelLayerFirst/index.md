---
layout: page
title: ModelLayerFirst
---



How do you start creating a Cocoa application? Many of us start with the user interface in Interface Builder. We then connect controls to methods and write the code to do whatever we want the user interface to do. This practice produces code that is driven from the GUI, and it becomes difficult to separate the Model layer from the View and Controller layers - or the non-interface related logic from the interface logic.

What if you turned this process around? What if you write as much of the model layer as you can, and only then start adding the user interface layer on top of that? This would help separate the two layers and make your program more flexible.

Don't ignore the user interface altogether though! You should have a generic idea of the user interface **before** you start writing the model layer; otherwise, it may be difficult to fit the user interface on top of it. You may want to create the generic user interface in Interface Builder and **put it aside** when you start writing model code.

Coding the ModelLayerFirst introduces a problem: how will you test your application? Because no GUI exists, there is no way to tell if your model layer is actually working. This is a perfect job for UnitTesting. Combine this with test driven development and you get a very nice coding process.

For more on this topic, see: http://c2.com/cgi/wiki?ModelFirst

*I'm definitely no expert on this topic, but I felt like it should be brought up. Please feel free to add a thing or two. -- RyanBates*

Perhaps a more important question to be asked is: What is the application domain?. 
With the answer to this and an understanding of the options, the right approach becomes obvious. 

*Interactive applications should start with the interface. The model is there to support the interface.
*Modelling/processing applications should start with the data or process model. The interface is there to display the model.

Deciding which domain your application fits into tells you how to start and what to concentrate on. -- MikeAmy

For more information on a similar topic, I highly recommend a book by Eric Evans titled Domain-Driven Design: Tackling Complexity in the Heart of Software. IMO, part one is a little tedious, but after that it gets much more interesting. -- RyanBates

Amazon link: http://www.amazon.com/exec/obidos/ISBN=0321125215

----

I am kind of wary of this approach since it is a kind of BottomUpProgramming since you develop the underlying code before you connect it with the conceptual limitations and realities of the problem that you are trying to solve.

If you create the conceptual devision between the UI-intensive objects first, then create the UI and connect them, you are probably better off since you are now taking more of a TopDownProgramming approach and you can now implement the components, re-factoring as needed, while you know that you are heading in the right direction.

Anything using BottomUpProgramming tends to kind of scare me since one often ends up writing a lot of unneeded code since the design doesn't help them stay on track.

This is definitely more of a personal opinion issue, I think, but it will be interesting to see what interesting points people bring up.

--JeffDisher

If you're doing TestDriven you can develop your domain model first and not end up with a bunch of unneeded code. You should end up with a good design and API.

--

I disagree.  The model should encapsulate the problem.  The UI (or UIs, in many cases) is merely a way of interacting with it.  If you've done your design right (at least at the high level), you should be able to to test-driven development of the model, then apply any UI to that - a terminal app, a cocoa app and all that guff, Web, DO, whatever you like.  If you can't develop a controller layer for whatever UI you want, you have a broken API.  Seriously.  

Of course, this requires a reasonable knowledge of the problem domain before you start...

--

Sure, what I said was just an example of something where the problem is specifically UI-intensive (and the underlying operation is some basic task - as is the case with most user-oriented apps).  In general, the key to what I was saying is starting at the *top of the problem*.  Define *top* as you see fit for what you are doing.

Plus, this definition for *model* and *controller* is based on Apple's unusual definition of MVC which is quite different from the way it is described by everyone else.  I don't find this one particularly useful since it seems to make rather arbitrary and useless divisions of code.

Definitely, the UI should be quite decoupled from the underlying code, though.

--JeffDisher
----

I'm curious what definition of ModelViewController you use if Apple's is unusual. Apple's definition isn't Smalltalk's original MVC, where the controller was a keyboard or the mouse, but I haven't seen any other definitions.

If the underlying operation of a "user-oriented app" is "some basic task', why would it be UI-intensive? I've been working on desktop financial applications for four years, with models that are anything but basic, so I don't have much experience with those user-oriented apps. I do however advocate getting the domain model right before writing any GUI code.

-- TerryWilcox 

Let's discuss this at MVCDifferences.

----

I like to do all layers.  Stub out the model API, maybe with nothing behind it but an NSDictionary.  Then write some view code to use data.  Then write some model code to hook them up.  Then iterate.  Writing the view code suggests features the others need.  Hooking stuff up to controllers can suggest things the view code and the model code need.

*I do something similar, I think. It's sort of SidewaysProgramming... or more commonly known as spiking. It works very well and very rapidly for me, and if I get bored of doing one part, there's always another part ready to be done.*

----

I whole heartedly agree with TopDownProgramming - defining the interface before the implementation - and, if you are doing test driven development, I do not think coding the model layer first violates this principal. In a sense, you are still programming the user interface first because the tests serve as the user and require you to define the interface before the implementation.

If everything worked well, the result is a thinner view/controller layer because the model layer handles most of the core logic. Of course, this also depends upon the type of application you are developing.

I am interested in the "SidewaysProgramming" approach, do you combine it with test driven development? Perhaps you could make another page on it and describe it in further detail. Thanks. -- RyanBates

*I don't do test-driven development, at least not in the classical sense. I've tried a few times, but it's just not worked out just yet. With my code, spiking serves to provide the tests, because the pieces are built such that nothing's stable until its neighbours are all fleshed out; so if section A relies upon section B, section B is (of course) subject to refactoring for a while... at least until the spike's become wide enough to move into outer features.*

*I also do spikes within spikes... e.g. I code via spikes to write a program, and each object is itself part of some similar drive, and each method within those, and so on. It feels rather biological, and for me has proven to be very effective; I code quickly, and my code is stable, and the program adapts to those tiny details of the problem domain that you didn't foresee when you started coding. It's research and development all in one, I guess.*

*A separate page is probably a task for someone who's put more conscious thought into the why.*

----

I am, and always have been, a ModelLayerFirst ( or BottomUp ) unit-tester. But then, my work's mainly OpenGL and simulation and what-have-you. I generally write my model into a separate library in C++, and then write mini programs ( sometimes GUI, sometimes CLI, sometimes just GLUT ) to test each aspect. But then, I also design things six-ways-to-sunday before I ever write a line or build the GUI. I've been burned too many times by writing the GUI before I understand the problem correctly.

Of course, once the GUI's written I sometimes have to backtrack. But so be it.

I'll tell you, though, synchronizing a C++ model with an Objective-C gui can be a headache, particularly when headers your C++ model needs aren't valid Objective-C ( think, typedef'd datatypes or functions called id, for god's sake ). It gets nasty, I tell you. --ShamylZakariya

----

I personally think my projects turn out better when I design the model first.  I think the best idea is to design the model with some vision of the interface, because that way the model is more efficient and is not just slapped together to fit the needs of UI.  In a sense, I feel the UI should reflect the model, as many have mentioned here, and my first tests are always on the command line.  I simply create a demo project in which things are done through direct commands to the model, then, I extend this to a full fledged GUI.  What I've found is that when you create the GUI first, the model "follows" it too much, and thus it becomes difficult to abstract it and possibly separate it, which can be very useful for porting purposes.  I think that the addition of the controller layer now even adds the possibility of being able to do them quite independently, since the model can have its way, doing things that make sense in a model-only universe, the interface can be user friendly, and the controller can bridge and gaps and discrepencies.

My two cents.

- Francisco Tolmasky

----

I did not read any of the above, but let me anyway add that how you should approach the design depends on the application! :)

----

I find it valuable to design an AppleScript object model first. This defines containment and properties of the major pieces without bringing low-level helper classes into the mix. Plus, it makes adding AppleScript support easier.

