---
layout: page
title: ModelViewControllerItis
---



Miguel de Icaza, Gnome and Mono project founder, had this to say during an article (< http://primates.ximian.com/~miguel/archive/2004/Sep-09.html >) critiquing Avalon:

*"Anyways, my problem is that it seems that Avalon is trying to do too much too soon.  It seems to be following the over-designed footsteps of Java's Swing: it avoids the over model-view-controller-itis, but it introduces its own pains and has the elements of a second system."*

What in the who now?  What is model-view-controller-itis?  

-KenFerry

----

Not sure, but it sounds like inappropriate use of ModelViewController.

-- RobRix

----

Figured that part. :-) But it isn't clear to me what either the symptoms or consequences would be.  If there's a known danger, it would be good to understand it/avoid it.  Right now, I can't think of a bad consequence of MVC thinking that would apply (if not avoided) to a layer like Avalon.  Seems like framework code is where you'd want to be most MVC-rigorous.

-KenFerry

----

Okay, here's someone who's down on MVC.  http://www.javaworld.com/javaworld/jw-07-1999/jw-07-toolbox-p2.html

I'm still trying to make sure I understand what he's saying, but this particular article doesn't seem real convincing.  He doesn't like MVC at all, thinks it violates encapsulation.

-KenFerry

----

Short summary of the above:  Holub thinks that view and model are too intimately tied to be factored into separate objects.  If you do so then you will break your encapsulation, and the result is that you will spend time and effort in keeping the view in sync with the model.    This whole thing goes along with his GettersAndSettersAreEvil article.  He isn't saying that every name object has to re-implement a text field, but that the code interface should look like "Hey name, display yourself on this canvas", in response to which the name object might instantiate a text field and tell it to draw itself on the canvas.

I'm trying to be charitable with Holub, and this is the worst case I can think of:

Take our typical task of binding a tableview to a model object.  What happens if we change the data that the object encapsulates (e.g. add a names array to the object)?  We have to change the model object, then change/add/delete array controllers that bind to the model, then change the bindings of the table columns that bind to the array controllers (say to point at newly created array controllers).  And that just handles the one table view, any other view that displays the model's data also must change.  Even worse, since IB binds objects through key-value coding - strings instead of method calls - we don't get any warning if we forget to change some bit of interface code to mesh the new model.  We cannot vary the model independently from the controller and view, so we didn't succeed at one of our MVC goals.

In Holub's approach, the data encapsulated by the model should never have been known to the outside world.  The model object itself should have been responsible for its presentation in the table view, so it's the only thing that needs to change.

This seems like a danger to be wary of.  It's probably an argument for displaying objects, as opposed to displaying the data of objects.  Also note that with bindings we have to edit objects at all three layers.  If we used the classical cocoa API we would have avoided screwing with the view, and we also wouldn't have been quite so tied to the specific bits of data provided by the model.  Just something to think about.

Arguments against Holub in general (not this tableview bindings case) include the possibility of using the delegation pattern and that accessors a) don't have to expose implementation, b) don't have to return raw data types like float, but can be objects.  Accessors really can be pretty indistinguishable from any other kind of message.  There's plenty more to be said, but I'm going to sleep now.

Also, I doubt this article expresses the problems that Icaza has with over-doing MVC.  This one's more like "MVC just plain sucks", and I think it isn't so hard to rip into most of what he says.  

-KenFerry

----

Icaza mentions Java/Swing, so you should probably find your answer there: http://java.sun.com/products/jfc/tsc/articles/architecture/

Swing uses a model and a view pr. GUI element, so a slider would have a model to store the actual value and a view to draw the value with whatever "platform look-and-feel" was installed.

This has close to nothing to do with MVC in the Cocoa framework.

That said, MVC is not the solution for all problems, as mentioned above, when you have a really close 1:1 mapping between the model and the view, it makes maintenance more difficult (because you always need to change three layers), it makes the code more complex, and it probably comes with noticeable run-time performance degradation.

And then there are things where everything but MVC would be one big hack, for example if you are writing a web-forum where people may want to view the forum through HTML or receive new messages through RSS. There is no doubt about at least having the view and model separated!

----

Slightly OT: Publishing on the internet, especially on IT and software engineering topics, has led to a bloated style of writing that drives me crazy.
Holub may have a point, but he writes like professors talk. On and on and on. He allows himself 9000 words when 3000 would have been perfect.
I hope he doesn't write software that way. Why can these websites afford columnists but not copy editors? Because editorial accountability
costs money, and wordy, pompous fools like Holub and I will write for free. And apparently don't have time or ability to edit ourselves.
But it isn't free for you: You just spent an extra half hour reading the two-thirds of it that is pure jerk-off self-indulgence.

Programmers may have to communicate with their managers like this, but not with each other. Or so I hope.
From the outset, Java seems designed to make programmers walk in nice straight ranks and files. That helps, but does not guarantee,
the production of robust software. That's important when an army of drones is building a huge enterprise system.
Extra hours spent in rigorous (sensu strictu) OO design *might* keep the whole rickety thing from later falling apart under its own weight.

*He isn't saying that every name object has to re-implement a text field, but that the code interface should look like "Hey name, display yourself on this canvas", in response to which the name object might instantiate a text field and tell it to draw itself on the canvas.* I guess that was his point.

Well, there's the Java UI design paradigm in a nutshell. I took up Cocoa precisely because I wanted to avoid having to do that every bloody time I wanted
to communicate in the interface. If people tell you often enough that you're a dog, pretty soon you'll be howling like one and peeing on tree trunks.

And the Bill Gates / ATM example. Holub has been breathing in this philosophy for so long, he's no longer able to stand up anything but straw men in his examples.

I don't suggest that software engineers be allowed to do whatever the hell they please or tell the customer how it's going to work for them, like it or not.
But when the customer changes his / her mind a year or two later and specifies that the system handle new demands, what they are
sometimes asking for is a new piece of freeware. Part of dealing with customers is getting them to understand that. The customer-is-king
culture is here to stay, so part of software design is really just about guessing what they're going to ask you a year or two from now.

Does "Hey, name, display yourself on this canvas" really take care of ALL those contingencies? Maybe so.
Maybe you SHOULD do it that way If all you're doing is building a glorified cash register or  -- tell me again, what else is Java used for these days?

Holub's arguments are by and large a relevant critique of certain types of RAD tools applied in certain types of environments, aimed at certain
kinds of enterprise-level software.

In fact, Holub (and those who think like him) want us to design software that has an *indefinite* degree of maintainability. Presumably because
that is the only way the marketing department can deal with promises to customers. The marketing department is in the business of engendering
unreasonable customer expectations. It's what they do. The tail wagging the poor dog in the previous paragraph. Embrace the pain.

----

Holub's argument, like so many sermons I've seen on ObjectOrientedDesign, relies entirely on a carefully-selected and overly-simplified example.  Adding a controller to handle displaying a string might be nothing but clutter, but what happens when you're trying to display information from more complicated objects?  I'm not interested in throwing delegates and notifications away and creating new subclass for every instance of NSTableView in my app.  I suppose I could modify my data classes to handle the display details instead, but then I'd have to start maintaining a slightly different version or subclass for each program, which is defeating one of the main purposes of modular design.  I'm not even going to think about the horrible tangle that would result if I tried to design my software the way he's suggesting in an application that provides more than one way to view the same data.  

With MVC, Apple throws the view junk in a framework and I can basically just leave it be.  I can throw the model junk in a framework and forget about it.  When the time comes to turn all of that into a real live application, I can just dust the two frameworks off and throw some logic between them, maybe taking some shortcuts with a few canned value transformers and the like, instead of wasting my time trying to teach a data type how to do glue code's job.  --SeanUnderwood

