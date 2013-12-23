---
layout: page
title: ExtendingControllerObjects
---

This is a newbie question.  I was wondering what is the trick to instantiate an object extended from NSArrayController. What I'm doing:

1) From the NIB, choose to subclass NSArrayController.
2) Create the files.

Now I would expect to be able to instantiate the new subclass, but alas I cannot.  I can, however, immediately instantiate a direct subclass of NSObject using the same procedure.  I'm just wondering what I'm missing here.  Thanks!

----
Create a new NSArrayController. Then in the inspector, select the Custom Class pane and select your class.

