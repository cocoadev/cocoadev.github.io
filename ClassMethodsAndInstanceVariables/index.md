---
layout: page
title: ClassMethodsAndInstanceVariables
---

Hey, I'm writing my first Cocoa program that retrieves tide information for my local area from the internet.

I needed to access a method from another part of the program (another .m file), so I made it a class method (with a + character) and #imported the .h interface file into the .m implementation file that needed to access the method. This worked nicely (though I'm not sure it's the correct way to do it), so I changed some other methods that I needed to access from across the application to class methods.

Everything was fine, until I changed a method that used an instance variable (one I created in the .h interface file) to a class method. This produced a warning on compile:     warning: instance variable `theVariableNameHere' accessed in class method.

Is there a better way for me to access methods in different parts of the application?

Many thanks :)

----

Trying to use an instance variable in a class method means something is wrong in your design. (Sorry to be so blunt!). You will need to think it through again. If you need the instance variable, it means the result from the "class method" can be different depending on its value; how can it be a class method, then? Maybe all your instances are supposed to have the same value for this instance variable, in which case you have a "class variable", and you would need to use a static variable with class-wide access.
Now, to access methods in different parts of the application, you can use NSNotifications, but they also apply to instance methods, so you will need to instantiate one instance of your class.
Maybe, you need also to think about the SingletonDesignPattern. --CharlesParnot

----

Hmmm... ::dazed and confused::

How might one normally go about making a routine accessible from any .m file you choose?

The instance variable is a reference to an object in Interface Builder, if that helps at all.....

:)

----

You might want to think about the design of your classes/instances, ensuring it follows the general principals of objective-c and cocoa.
Generally, we instantiate one instance of a class, and then call the method eg.

MyUploader *uploader = [[MyUploader alloc] init];

[uploader putFile:@"~/desktop/myfile.txt"];

(MyUploader being your class, 'putFile' being your method)

----

This is one of the hardest things to understand when you are starting cocoa programming and one many still don't really grasp so don't worry - you arent the only one!  Singleton Pattern is one way to do this if what you need is to access a method in another class - this means that you insure through a custom init call that there is never more than one instance of that singleton object.  Without the singleton declaration you get a different instance each time and this messes up things like UI calls that may be managed by that object that you now have numerous instances of.  If you just need variables then you can use accessor methods or use a bridge such as Notifications or StandardUserDefaults to pass the variables around.

EcumeDesJours

----

Thanks, EcumeDesJours - yeah, I was raised on BASIC, RealBASIC... and Visual Basic (your queue **(cue? or are you trying to make a pun?)** to look aghast)! I've heard that Visual Basic teaches you some awful stuff that you have to unlearn before moving on to a competent language, especially an OO language, and I believe it ;-). But what I've done in Cocoa with minimal instruction (just basic ObjC syntax and AppKit header files) in a couple hours would have taken quite a bit longer in BASIC, so I'm lovin' it already.

I've ordered the second edition of the BigNerdRanch book on Cocoa, Cocoa Programming for Mac OS X, and it should be arriving... some day. Until it arrives and I can read it with some depth, I'll be content with my partially-functional app to get tide info ;-) it still beats the website... or Mr Tides! bah!

----

I'm just writing my first program too. I've solved this problem by creating those instance variables (which refer to the IB objects that I'll need in differing parts of the app) in the NSObject I've used to connect the Main Menu actions to. Since all my other objects are created by user action on the Main Menu (or by user action in a window which was created by Main Menu), then the Main Menu object, when creating an instance, simply needs to pass an instance variable in a custom -initWithMainMenuHandler:(id)instanceOfMainMenu method. The created object can then interrogate the Main Menu object which has a method for each instance variable which will be needed. Probably not the most elegant way of going about things though.

ArthurPeake

----

Other recent discussions of this issue:

UsingAccessorMethodsWithIBOutlets
HowToTransmitDataBetweenClasses

