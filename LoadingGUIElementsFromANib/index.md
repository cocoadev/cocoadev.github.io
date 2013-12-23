---
layout: page
title: LoadingGUIElementsFromANib
---

I'm working on a program in which some of the GUI elements are necessarily dynamic and need to be created programmatically at run time. E.g. not the place for IB, though I use IB to build the rest of the GUI. I've already written a crude layout manager for vertical stacking of subviews as well as scroll view management -- enough that I can add an arbitrary number of controls procedurally and they're layed out correctly (in a vertical stack). All's fine there.

The actual GUI elements which are packed into the layout are written by hand (not in IB) and consists of stuff like a slider + 2 labels, one for name one for value; a checkbox, a button, etc etc. Simple GUI elements. I make the calls to setTarget and setAction and setAutoresizingMask and whatnot procedurally as well. But, building them in code seems a bad idea since localization is non-obvious and layout is, at best, a tweak/compile/test loop that takes way too long. I'd prefer to just build it in IB and have my code suck them out of a nib file and pack them into the layout.

BUT, here's the rub. I can't figure out a way to make a GUI element in IB which can be instanitated, complete with layout and connections, an arbitrary number of times. E.g., it's easy enough to make a subclass of NSView, instantiate it, pack in a few labels and a slider and make outlet/action connections for THAT instance, but at runtime I might need five, or thirty. or none. I know that NSBundle provides the classNamed: method to give me a class object for instantiation but that gives me only the class, not a built out GUI element. Does object copying/cloing work for complex GUI elements? Could I make one instance and just copy it ad nauseam? I'd worry that outlets and actions would be messed up if it weren't an intelligent deep copy.

So... am I missing something obvious here? My current approach (building the GUI elements in code) works, so I'm not dying here -- but I recognize my current approach for being a stopgap and I'd like to take a less hacky approach if at all possible.

ShamylZakariya

----

Have you tried creating a custom view in IB and then deploying that as much as needed?

My understanding is that the subclass is just a class -- that adding widgets to it can only be done to it when it's instantiated. Unless there's a way to take an instantiated subclass of NSView (with actions/outlets/etc set up ) and to then to freeze dry it as a class. --ShamylZakariya

Let me be the first to agree that Apple (NeXT) really seems to have missed a spot, when focusing so much on InterfaceBuilder and NIB files -- it's really a pain to create dynamic GUIs.

However, for your case I think you should start IB, select an "Empty" project, then from the "Containers"-palette, drag a CustomView to the document (i.e. where you have File's Owner and First Responder). If you double click its icon (in the instances) then you get the view displayed in a window, and you can drag gadgets to this view, set up springs and connections.

Then add an IBOutlet to whatever class is responsible for creating your dynamic GUI, which should hold the custom view we just created, change the class of File's Owner to this one, and set up a connection.

Now you should be able to load the NIB repeatedly, and each time, the custom view will get instantiated (with children and connections), and a pointer is available through the outlet.

----

That sounds like an interesting way to go about it -- I'll try it for completeness' sake. I decided last night, however, to try to use a table view and override the NSTableColumn dataCellForRow: method, such that I can put in a slider on one row, a checkbox on the next and so on as is appropriate. I'm concerned about performance, however, since in my experience NSTableView's a bit of a hog for live updates. NSOutlineView seems even worse :(  This part is just one aspect of the frontend for a physics simulation (for robotics AI) and as such, as much CPU as possible needs to be devoted to the engine, not the GUI. Shucks. I'll try both and go for whichever seems to be cleanest in implementation and easiest on the CPU. 
--ShamylZakariya

