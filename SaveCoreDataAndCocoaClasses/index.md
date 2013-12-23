---
layout: page
title: SaveCoreDataAndCocoaClasses
---

I am developing a graphical application where i use Core Data to store the logical information of my entities, and normal Cocoa classes to store all the graphical information (colors, positions, sizes,...). The graphical entity has a pointer to the core data entity. Is there any way i can store the managedObjectContext together with my custom attributes? 
----
The answer depends on how your objects are being used in the diagrams.  If an object can be used in many contexts then the answer gets more complex because the graphical info (colours, position, sizes) must be in "context of the object's usage".   For example is a Flow Diagramming application an object (eg a process) might get used in a number of different Flow Diagrams. Keith

----
Each object is only used in one context.

----
It is the 4th or 5th time I read your question since yesterday, and I still don't understand it, or at least not to the point where I feel like I can have a chance to give a useful answer. Could you give more details and be more explicit about what you want to do. Sorry :-)  --CharlesParnot

----
Another way of looking at this is as follows: The Model-View-Controller design paradigm. Are you following it? There's no reason at all why all your persistent data can't be stored in an NSManagedObject or a subclass thereof. The display of these elements should be handled by a separate 'view' (which is a separate object from your 'model'). I may be misunderstanding your post, but I just got the impression that this may be part of the confusion. All persistent data should be in your managed object. When the object needs to be viewed, you create your 'view' object and plug it into your 'model' object to get the data to be represented by that view. One view instance per model instance or even one view to represent many models at once (depending entirely upon your project).

I agree with Charles: More information is definitely needed here.I'll add that it might be helpful for you to review HowToAskQuestions to get a good idea what others need to understand your project and your problem and give more useful answers.

