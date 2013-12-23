---
layout: page
title: MVCInDepth
---

I understand the basics of the MVC (Model-View-Controller) paradigm, but when one starts creating large applications that require multiple controllers, the distinction between the three object types seems to fade for me. I was wondering, does anyone know of an in-depth source for the MVC design pattern? Particularly targeted toward larger projects.

Is it safe to say that data (model) objects "talk" to no other objects except those which they create? In other words, they are completely independent and self contained. It would be nice to say that data objects cannot contain objects in the AppKit framework, but I believe one can rightly store an NSImage in a data object. Would NSDocument be considered a controller object or a data object?

Apple added the NSController object to work with bindings and such, but the problem is it only works in 10.3 so I'm trying to avoid using it. Perhaps if I use it though, It will help me understand the entire concept. I like to think of controller objects as holding the "staring" data objects. The data which the controller objects contain (over the entire chain of data objects) adds up to all the data objects retained in the entire application. The controller objects set the value for the view objects using the data it possesses and manipulates the data based upon the user input. Is this correct? Do you need to create as many controller objects as there are windows in the application? What if it is a very simple window that only displays a progress bar? Is it bad to create a controller object that does not manage a view object? Such as a controller that manages only a network connection.

I realize the MVC paradigm probably doesn't fit perfectly into every situation, but any source someone can point out to help me better understand MVC would be greately appreciated! Thanks.

-- RyanBates

----

Google:  Model View Controller

lists a number of references.  Reading a few of them should help you.  These looked pretty good:


* The Model-View-Controller Design Pattern (Apple Documentation)  [http://developer.apple.com/documentation/Cocoa/Conceptual/AppArchitecture/Concepts/MVC.html]

* Model, View, Controller  [http://cocoadevcentral.com/articles/000003.php]

* Model-View-Controller Pattern  [http://www.enode.com/x/markup/tutorial/mvc.html]

* The Model-View-Controller Architecture  [http://rd13doc.cern.ch/Atlas/Notes/004/Note004-7.html]


----

Thanks for looking that up! I too did the same search, but all of the articles I found were either too simplified or they differed from Apple's view of MVC:

http://developer.apple.com/documentation/Cocoa/Conceptual/ControllerLayer/Concepts/MVC.html

In the diagram halfway down the page it shows that the model and view never interact directly, but in several other diagrams of MVC (non-Cocoa ones) it shows the Model interacting directly with the View. That's very confusing to me, anyone care to clear it up? :)

***Update:** Somehow I missed that link to the Apple docs. The part where it talks about Model-Controllers and View-Controllers actually clears a lot up for me. Thanks!*

-- RyanBates

No problem.  MVC is not the easiest thing to understand the first time you see it!  The SmallTalk (Squeak) people have used this pattern
the most.  It is used fairly often in Java also.  Squeak Wiki  [http://minnow.cc.gatech.edu/squeak] has some links to some MVC examples
also [http://minnow.cc.gatech.edu/squeak/353] that would be good examples in Cocoa (or any other framework).  -- Bruce

----

With respect to NSImage being in AppKit: If you e.g. create an image database, attach CD cover art to your music player or similar, only a path (i.e. NSString) will be in the model, and the controller will instantiate the NSImage from this path, and set it as a value in the view.

If you on the other hand is working on an image processing application, then you may need to introduce your own image manipulation class and keep instances of this as part of the model, as your model basically work with picture data -- but to actually show the picture data, you would probably still need a controller, e.g., the output device could be a terminal, and thus the controller will convert the picture to something which can be displayed in that environment.

