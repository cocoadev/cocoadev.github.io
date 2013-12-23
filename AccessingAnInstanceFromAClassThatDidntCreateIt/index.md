---
layout: page
title: AccessingAnInstanceFromAClassThatDidntCreateIt
---

Hi, I am getting to grips with Cocoa/ObjC and have the following conundrum...

In my simple project I have three classes I have created, TWO of them I am using as controllers. One is a controller for the main interface, the other is a controller for a sheet I am using for some simple options.

The third class is where all my more serious gubbins is, it basically uses some array's and some other simple things, and stores the data I am using (just for some NSTableViews) in my app.

Here is the problem,

when controllerOne gets awakeFromNib called, it creates a nice instance of the thirdClass (the 'backend' gubbins class), and calls it, lets say thirdClassInstance.

This is great, controllerOne can call anything it wants in thirdClassInstance, after all, it did instantiate the class.

However, now I have written controllerTwo, and controllerTwo, although not as complex as controllerOne, still needs to call a couple of accessor methods I have written in thirdClass. But controllerTwo knows nothing of thirdClassInstance (it didn't create it) and so is not playing along.

How can I make an instance of a class available to more than one other controller / class instance?

This is what I am doing for controllerOne...

under the #import statements in controllerOne.h, I have added the class thirdClass thus -    @class thirdClass;

I have given controllerOne a new instance variable with which to create thirdClassInstance thus -     thirdClass *thirdClassInstance;

and have instantiated it on awakeFromNib thus -     thirdClassInstance = thirdClass alloc]init];

Now what do I do for controllerTwo to access thirdClassInstance?

Thanks for any help!

-Peter

----

This one always gets the newbies (it got me!). Check out [[SingletonDesignPattern. Basically, you want +sharedIstance methods all around.

----

Don't want to seem stupid, but I'm not sure I understand everything in the SingletonDesignPattern. It seems real complex to me, is it all necessary to allow access to my instance from another class? 

I had imagined it would just be a slightly different way of instatiating the class, but despair and confusion is setting in :(

----

It's not *that* complex, and it *is* just a slightly different way of instantiating the class. Instead of calling thirdClass alloc] init] multiple times, you just call it once from thirdClass* sharedInstance method (after first checking firstClass == nil), and then call [thirdClass sharedInstance] the rest of the time.

----

Why can't you just make a     -(thirdClass *)thirdClass accessor in your larger class, and call it from controllerTwo?

----

I wouldn't recommend that. Here's what it boils down to (and this is what I do for internal shared classes).

    
 @interface ThirdClass
 ...
 + (ThirdClass *)sharedInstance;
 ...
 @end
 
 @implementation ThirdClass
 
 + (ThirdClass *)sharedInstance {
     static ThirdClass *sharedInstance = nil;
     if (!sharedInstance) {
         sharedInstance = [[self alloc] init];
     }
     return sharedInstance;
 }
 ...
 
 @end


You can do this for your other two classses if you think you'll need to (that would probably be a wise idea as a controller is usually a shared object, and as your application grows you may discover you need to access them from even more classes).

Now, to send ThirdClass a -description message, do this:

    [[ThirdClass sharedInstance] description];

Instead of initializing ThirdClass and then storing it as an ivar.

Note that this design pattern (essentially) mirrors that in [[NSUserDefaults, NSFileManager, NSNotificationCenter, NSApplication, NSHelpManager ... It's everywhere.

----
You could just link up all three controllers in IB...
Use the nib to instantiate them.. give each IBOutlets to the others, and then link them up in IB.
You can add classes to IB by dragging the .h file from PB/XCode.. and then you instatiate your controllers by choosing the class, and the choosing instantiate from the Classes menu.

----
This essentially limits you to one nib apps. Using a shared instance or adding an accessor method to the owning class are better solutions

----
I agree with the previous.  I don't think the information given by the original poster is enough to say that the singleton pattern is appropriate.

----
From the information given by the OP, IMO a singleton class is way overkill if he just needs to use "a couple of accessor methods" from the class in one other object.

----
(Original Poster)Yes, all I need to do is use a couple of accessor methods of one of my instantiated classes... only problem is I have TWO other classes and obviously only one of them alloc'd and init'd the instance... so I need some way to, I don't know, introduce the ThirdClass to the other class that did not create it.

All I'd like to do is change a couple of things (in an NSMutableArray) in the programmatically instantiated class. (Thanks for all the advice, but some of it conflicts, and I'm still confused!)

----

Since you say 'programatically instantiated class', do you have instances of the other 2 classes in your nib? If so, the easiest thing to do would be to create an outlet (I'll call it owningClass - you can call it whatever you like) in the class that does NOT instantiate ThirdClass and connect it to the class that does. Then add an accessor method to the class that instantiates thirdClass as described above. Then you can say 
    owningClass thirdClass] doWhateverWithThirdClass] in the class that doesn't create thirdClass. You can call any method in thirdClass that way.

The accessor for thirdClass should look something like

    
 -(thirdClass *)thirdClass
 {
     return thirdClassIVar;
 }


The reason you've been getting conflicting advice is there's more than one way to do what you want, and the best solution depends on what the classes are doing. Is the class creating thirdClass a document class, and the [[NSMutableArray pertaining to some data in your document object? If so, it should rightfully own thirdClass, and control outside access to it through an accessor method. Is it a truly shared class, like something to do with your application's preferences? If so, the shared instance way might be best. It really depends on how complex your application is.

----

See also HowToTransmitDataBetweenClasses

----
awakeFromNib is late to be instantiating classes that dont' need nib based objects. If you have 2 controllers in the nib and one creates a class in awakeFromNib and another one calls it in awakeFromNib, the 1st object might not have gotten around to creating the class yet. It's better to do your class instantiation in init instead.

    
 -(id)init
 {
   self = [super init];
   if (self)
   {
     thirdClass = [[ThirdClass alloc] init]; //2005-01-03: edit to remove a (spurious?) retain
   }
   else
   {
     [self release];
     return nil;
   }
   return self;
 }

