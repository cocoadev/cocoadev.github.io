---
layout: page
title: ModelViewController
---



ModelViewController is a programming model widely used in Cocoa.

When something follows the ModelViewController model, it separates its classes into three categories: Model, View, and Controller.

Model classes are classes which handle data and operations performed upon it. Things such as reading, writing, archiving to file, et cetera, will all be handled by these classes. You might have a model class to represent customers in a small business' customer database, with data containing their name, how many purchases they have made, what those purchases were, and methods which allow you to record another purchase, write to file, archive onto a different drive, and to change information about them (shipping address, for instance).

View classes are classes which handle drawing to the screen. These classes can often be almost infinitely reused without subclassing. In the small business customer database application, a view class probably wouldn't have to be written specially - NSView would probably do in most situations, along with the application's NibFile.

Controller classes tie model and view classes together. They tell the view classes what to draw, and the model classes what to do. Back in the customer database application, a controller object could be used to tell the view objects to display information that the model objects are handling.

----
**Or to put it another way: **

The model is responsible for managing the data, the controller is responsible for coordinating the exchanges between the view and the model, and the view is responsible for representing the data to the user and accepting user input. In other words, when the user interacts with the view, the view tells the controller, and the controller informs the model of changes; then, if necessary, the model massages the data and informs the controller of any changes which the controller then passes to the view. The view will then update if necessary and wait for more input from the user; thus, perpetuating the cycle.

----

Some hypothetical Objective-C code that might be found in the controller class:

    
[someViewObject writeTextToScreen:[someModelObject customerName]];


Which would, obviously, write the customer's name to the screen.

Applications that use the ModelViewController programming model do not always need to adhere quite so strictly. In some occasions, such as games, an application might combine View and Controller objects.

ModelViewController programming is useful, because it optimizes for code reuse, and other ObjectOrientedDesign concepts. It provides abstraction between the interface and the data, which allows you to reuse the Views almost all the time (and View classes can be difficult to implement if you don't have help), and just code the application specific parts.

----
Apple has an article on the Model-View-Controller design pattern at http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/chapter_5_section_4.html 

----
There's even a song to get the pattern stuck in your brain written by Apple's James Dempsey. ( http://www.oreillynet.com/pub/wlg/3533 )

