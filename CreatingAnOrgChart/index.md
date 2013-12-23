---
layout: page
title: CreatingAnOrgChart
---



I'm trying to code a drag-n-drop org-chart into my app. Basically, you'll be able to create departments and groups within those departments and be able to drag your personnel between them. Here is a basic drawing of what I want to accomplish:

http://homepage.mac.com/tylers/Development/images/OrgBoard-Sketch.jpg

Any ideas on how to accomplish this? I've created a window with a custom NSView. How can I get the window to detect what "card" was clicked?

----

That's a huge question. Basically you're asking how to design an application. It's not really a Cocoa-specific question; I think it's probably not the right thing to ask here. But here's a pointer or two. Each "card" is an object in your data model, which contains further objects such as the person, the department, who reports to whom, and so forth. You need to develop a structure that models the real-world structure that you'd like to portray. Forget about graphics for now - that's just a visualisation of the underlying data. Read a good book - The Hillegass one is good, but seems to me you might need to go down to a more fundamental theoretical approach that discusses data modelling in general. --GrahamCox

----

Actually, the backend data modeling is already done. I've already read the Hillegass book (though it has been a few years) and understand Cocoa programming concepts (though, admittedly, I'm a little rusty). I'm just looking for input on how more seasoned programmers would go about tackling the graphics side.

----

The question is still very broad (and not really a Cocoa question, it's a generic OO design question). Do you want to lay out the chart automatically? If so, look into "directed graph layout" algorithms. For each entity in the chart you need to claim a rectangular space in which to draw it. How you do this could be complex, but let's assume you have that figured out. Eventually you want to end up with a list of objects with each representing one entity on the chart, positioned in the co-ordinate space as appropriate. Each object has a reference to its counterpart in the data model, so it can pull values out of it when required. Your view then just needs to iterate over this list and ask each entity to draw itself. It'll do this by using a combination of standard text and graphical techniques, but using the data it reads from the data model. That will take care of rendering the model. The other side of the coin is how to handle input, such as clicks on the objects and dragging them (or parts of them) around. The view provides you with basic methods for handling mouse input and receiving drags, but you will still need to do quite a lot of work - detecting which entity was hit and handing off to that entity to further refine the process.

You could make each entity a subview which will take care of much of both drawing and handling input, but if the charts get complex this might not be the most efficient approach. Even if each entity was a view, it's unlikely that you'd go as far as making each element within the entity a view - that would be seriously poor performance-wise. So at some level you will have to take on the task of doing pseudo-view like tasks, such as keeping track of areas within each entity that correspond to a particular piece of data, detecting which area was clicked and so forth. You also have to deal with the connection between the graphical representation and the data model (probably through some sort of intermediate controller object). None of this is hard but the amount of effort shouldn't be underestimated. Cocoa won't help you all that much to solve the "big picture"; it helps by providing containers and lots of low-level stuff. Unfortunately I feel this comes into the category of "obvious to an experienced programmer", and one of those things that is much easier to demonstrate than to explain. Since you have a working back-end, build on that in a bottom-up fashion, but keep an eye on the needs of your view with respect to rendering, handling mouse input, etc. Sorry this sounds so vague, but the question is too imprecise to answer otherwise. --GC

----
Just my two cents, having written something vaguely similar, use views until you are sure that the efficiency of your app will suffer, premature optimization and all that. If you need to minimize views by writing your own drawing code, but find that you replicate much of NSView's functionality, then I would bet that NSView would end up being more efficient anyways. I have an application which lays out hundreds of views each containing 5-100 subviews. Needless to say, drawing is a bottleneck, but I need text editing within the views, and customers would be miffed if my app behaved non-maclike. I just use NSImage to cache various parts of the view, take advantage of NSScrollView / NSClipView features, and generally take care with my drawing code. The app behaves quickly on a G4 Titanium Powerbook 550mhz, so new macs should run it just fine (*smiley*). Any nontrivial drawing will be a bottleneck in just about any API-OS environment, so do your best, and trust in Cocoa. Looking at your example, I suspect that NSView's would fit your needs just fine. If your view can zoom out to show an organization of several thousand people at once, then you might want to have a special high level drawing algorithm blah, blah, blah, you get the idea. -JeremyJurksztowicz

----

Yeah, on balance I think I agree. The only thing I can see could be an issue here is that if each element within a card is a view, dragging an employee could become a bit awkward - I imagine you'd want to drag the name, image and job title out as a single draggable item. If each bit was a separate view it becomes harder to handle that. --GC

