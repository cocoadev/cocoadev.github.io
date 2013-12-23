---
layout: page
title: ExchangeDataBeetweenClasses
---


Let's say i have two classes "class1" and "class2" and they need to know about each other.
More specifically, they needs to share variables.
Let's say i have "var1" in "class1" and "var2" in "class2".
I tried #import "class1.h" in "class2" and vice-versa but it is working just for the first class (the second one say "va1 undeclared".
I've read in a previous post that the use of import was not recommanded (it's the only way of working with multiple class i've found on books!) and the use of the @class d�claration was prefered.
So i tried to understand how "@class" is working but as i understand, it just allows you to create instances of your other class.. not to get info from your previously instanced one.
So what is the best way to get "var1" from "class2" and "var2" from "class1"?
I was thinking about creating an "AppController" class to hold all the globals but is it the right way? - StephaneDassieu.

*
After  a little search, i've found some info. To make a global variable i have to declare it with "extern" (why the hell none of my books are talking about this?). But i've also read that it is not recommanded. It looks like i have to make a specific class controller for my two classes and store the variables in it. Gonna try this.
*


----

It looks, from your post, that there are a few things you're not grasping about Cocoa. I assume you've declared var1 in class1 as follows:

    @interface class1 {
    someType var1;
}
@end


Now there's not just one instance of var1; instead, every object you declare of type class1 will have its own, personal copy of var1.

    class1 *myVar;
myVar->var1;


If you want to use just one copy of var1, you'll instead need to declare it at the top of the .c file that implements class1.

    // class1.c

someType var1;


Having done that, if you want to use the same copy of var1 in your class2 code, you'll need to declare it as     extern in your class2 .c file.

    // class2.c

extern someType var1;


However, a better way (more ObjC-like and less prone to linker errors) of getting access to that global is to create a getter and a setter as class methods in your class1.

    // class1.h

@interface class1 {
}

+ (someType)getVar1;
+ (void)setVar1:(someType)newVal;
@end

// class1.c

import "class1.h"
someType var1;

@implementation class1

+ (someType)getVar1 {
    return var1;
}

+ (void)setVar1:(someType)newVal {
    var1 = newVal;
}
@end


Now, in your class2.c file, you can simply import     class1.h and use     [class1 getVar1] and     [class1 setVar1:...] to access class1's var1 global.

(Note that this code was written off the top of my head and may not compile verbatim...)

----
"It looks, from your post, that there are a few things you're not grasping about Cocoa." -> you're perfectly right, i didn't catched the "declare the variable <out of> or <into> the class thing. But it's weird that none of the books i bought are talking about such common principles (i have BookLearningCocoaObjCSecond and BookCocoaProgMacOSX). Thanks, it's a lot more clear in my head now and will sure help a lot of newbies like me. - StephaneDassieu.

*They aren't mentioning globals because globals are rare.  Beginners have way more important things to learn.*

----

*..I've read in a previous post that the use of import was not recommended..*

That must have been off of a GNUStep list.  The GNU people have fights about #import, but people working on Macs don't, generally.  Go ahead and use #import.

*And, in any case, the debate has been resolved. At one point the use of #import was deprecated in gcc, but it no longer is. So the only reason GNUStep people or anyone doesn't use it is pure stubbornness / habit.*

I don't think that has anything to do with it though, the OP was talking about the use of #import vs. @class. The rule I go by is in my header file, I only import my superclass' header, and any other declarations are done with @class. Then I import any headers I need to use methods from in my class' implementation.

----

Mmmmh there is still a thing i don't get, let's go back to my example :

* Class1 is a custom view. I made it in IB and it's on my main window.
* Class2 is also a NSView subclass but as been made in PB.
* When i press a button, it add an instance of Class2 as a subview of Class1.
* When i click on one of this subview, it close it (let's make simple).

This classes needs to know each other and share some info. When i'm adding a Class2, i need it to draw his number so i need to know how many items i already have in the array storing the data of my Class1. And when a subview is closed, i need to tell Class1 that something has been removed so it can reorder the view and bla blah blah.

**Here is my problem :** i don't really understand how to use "instance classes" and "method classes" ("instance variables" and "method variables". I don't know why i should use any class method to do what i want but if i don't, i can't access Class1 methods from Class2.

*For example, when i delete a subview, Class2 send a [Class1 removeBoxFromPlan: boxIndex] message. As i'm am asking Class1, it means i'm asking a method class and not an instance one (i don't know how to ask instance classes). So my method will be +(void)removeBoxFromPlan:(int)index. But in that method, i can't access any instance variable, so i have to declare my variables as method variables by placing them before the @interface too...*

is it normal? It don't looks very neat to me and in fact i'm almost sure i'm doing sh*t..


----

i think maybe you could approach it another way

classes build relationships with each other.  in your example one view is a "Sub View" of another.  this is a relationship.  the Parent can always refer to the child, it retains an array of its subViews, which are accessible through a method in the parent view, for instance.

this is where "nesting" comes in.
if your parent needs data from the child (an integer for instance), then you include the .h file in the .m file of the parent (so that the parent instinctively knows the makeup of the child and expects the child to support its own methods) and where you need to refer to data from the child, you do this:

[ parent someMethodThatNeedsDataFromtheChild:[child themethodToGetDataFromTheChild]];

the nested method is where we refer to the child, we don't ever ask it for access to its instance vars, we just ask it what the value is.  the main method is expecting a certain data type, and as long as the child returns that type of data, then it will work.  For the parent Class it doesn't matter what the child's innards look like, just that it gives the correct answers.
its almost like the parent is using the child as a reference, asking for the data only when it needs it.

this way you never have to murder the encapsulation of your classes.  

this is a cornerstone of Object Oriented Programming. the idea is that each class does its thing, and does it as good and as bug free as possible, and we get information through a "messaging system", that relies on the relationships we build between our objects.  This makes OOP startlingly powerful.  you should only Break these tenets when you have no choice.  in the example you cite, access to another Object's instance vars is BAD.

----

You sound like you're trying to do something incredibly complex for someone who admits to not understanding instance variables yet. Why are you trying to add a subview in the first place, and why does removing it when it's clicked on make sense? Perhaps if we understood what you're trying to do, we could help you do it properly.

For a start, you shouldn't be doing *anything* complicated in any View class. If it's not a simple matter of displaying something on the screen, it belongs in a Controller object: see ModelViewController. What *should* be happening is this:


* You click a button, and it sends a message to a Controller object (perhaps     -addExtraSubView, though something descriptive based on what the button is there for would be better, like     -addNewIcon: or something)
* The Controller object should have a reference of all the subviews previously added, and a link to the superview it's adding them to.
* The Controller object, on receiving     -addNewIcon:, creates a new subview, set up as you want it, and adds it to the superview. It also updates the Model to reflect this.
* The new subview should have an action, linked back to the controller object, that sends a message when you click on it (like     -removeIcon:)
* When the Controller object receives a     -removeIcon: message, it figures out what the subview in question has to do with the Model, then removes the subview. It also updates the Model appropriately.


For instance, if these "icons" represent objects stored in the Model, the Controller must somehow map each subview to the object it represents. That way, if the subview is clicked on, it can remove the correct object in the Model; equally, if the Model is updated some other way, it can update the subviews to reflect the new reality.

I hope this helps somewhat.

----
Sure, it helps a lot.
To be more precise, i'm trying to do an Automator like interface (see AutomatorLikeUI).
Here what i've done:


* My Class1 is "PlanView". It's a custom view made in IB and added to the window.
* My Class2 is "BlocView". It's a custom class i can add to "PlanView" on the fly.
* When i add a BlocView to PlanView, i want it to go under the previous one as in a list. - *so i have an yPos variable to hold the Y value of the previous BlocView in PlanView.m*
* When i add a BlocView to PlanView, i want it to draw its index (number) in the top left circle (as in Automator). - *so i made an accessor/mutator to the index value in BlocView.m*
* When i click the the close box of a BlocView, i want it to be remover from PlanView. - *so i have a removeFromPlan: method in PlanView.m*


As you see, i didn't used a Controller class. I just set the PlanView 'as the controller'. (Tell me if i'm wrong to do so, maybe it's better to create a third class to control the Plan and the Blocs?).

I modified my code regarding to tour advices (thank by the way). So now, BlocView has an accessor/mutator to change it's index number (i'll need this: if i have 3 blocs numbered 1-2-3 and i delete the #2, i need PlanView to update the index of #3 to #2 to have 1-2 and not 1-3, and i also need to redraw the view to remove the blank space but it's another story)
Thanks to this, my code is a lot cleaner, no more class methods nor class variables.

My problem now is when i made PlanView in IB, i didn't added it an outlet.
How can BlocView sends messages to PlanView?? It don't even know PlanView exist...
In fact i'm trying to do the point "The new subview should have an action, linked back to the controller object, that sends a message when you click on it" of your list.

I tried self superview] removeFromPlan]; inBlocView.m but it don't works (removeFromPlan is a method in [[PlanView.m)
Should i add an outlet to PlanView? How to connect this outlet to BlocView? - StephaneDassieu

----

Firstly, yes, you should make a Controller object, it's good design. Ideally, you don't want to roll your own View class at all; you should be able to get by with Controllers in most cases. Let's ignore that for now, though. Your case may indeed be a special one.

If you're creating BlocView on the fly, you'll need to manually tell it what action to take on being clicked. Ideally, you would derive BlocView from NSControl, not NSView; this lets you set up a standard action with     setAction: and     setTarget:; otherwise, you will need to duplicate this functionality by adding an outlet to BlocView that PlanView sets to point to itself when it creates a new BlocView.

----
*otherwise, you will need to duplicate this functionality by adding an outlet to BlocView that PlanView sets to point to itself when it creates a new BlocView.* --> this is exactly what i'm trying to do. But regarding to HowToUseOutlets post, it looks impossible! Can you post a generic sample code please?
I already added **IBOutlet NSView *plan;** to planView.h but what now? I can load this outlet in IB but i cannot connect it to BlocView as there is no representation of it in IB.. - StephaneDassieu

*update -> Damn, searching for 2 hours without any results.
NSNibOutletConnector *nib = [[NSNibOutletConnector alloc] initWithCoder:]; send an error "NSNibOutletConnector undeclared�". I really need some help to do this : i haven't found anything on the internet about how to create outlets on the fly.*

----

you could seriously benefit from doing the tutorials.
first you need some ground level understanding of Interface Builder, you can't get that here, nor from going on hunting trips through the ref docs.  You could also use some "Language training."  you need to get comfortable with what is meant by such things as: class, object, method, outlet, action, instance... just to name a few. and the best way to do that is to go through the introduction to cocoa tutorials.
Otherwise, you're going to be stymied by the basics, and when people try to help, you won't grasp what they are really trying to say.

go to the documentation in Xcode
found under the help menu.
under the Library heading, click on Cocoa.
at the top left of the resulting page, click on Getting Started.

do everything it tells you to do.

I would also consider some alternate ref docs. "the TAO of Objects" was very helpful for me to grasp the importance of all the insane things one has to do in OOP.  It isn't a language book, it simply speaks to the motivations behind Object oriented programming. its a short read, and when i read it, it put me in a good frame of mind for understanding objects.

another WONDERFUL book to get is "Cocoa Recipes", by William Cheeseman.  an in depth look at making an application from scratch.  Its out of date by now, but everything it does is important to learn.

----

Damn.. you're last post is kinda rude to me.. I already made a lot of tutorials and i also read 2 books (BookCocoaProgMacOSX and BookLearningCocoa) and also bought BookCocoaInANutshell... and i read the ObjC.pdf from Apple too. I think i'm suffering from being raised on REALbasic so i've learn OOP the wrong way. And i'm French so it's quite harder to learn in a foreign language.

I know i did shit with class methods and i corrected myself, now it's looking like a 'clean' code.
But none of the books i read are talking about creating object dynamicaly and dealing with them. All the examples and tutorials i made are made using IB a lot. I think i got the 'outlet/action' thing, but how am i supposed to connect a subclass of a view i am dynamically instanciating?

In fact the two books i have don't even talk about the NSLib class because they were written before 10.3 - StephaneDassieu

----

I don't see the previous post as being rude; your questions are pretty basic, and indicate that whatever you've read, you need to go over it again. Cocoa is a large subject and you're not going to 'get' it in one reading. In addition to reading and doing tutorials, look at lots of sample code. /Developer/Examples/AppKit/ on your hard drive is a good place to start.

As for your question, there's nothing magical about IBOutlet<nowiki/>s - they're just instance variables prefaced with a IBOutlet keyword that InterfaceBuilder looks for and adds those names to its list of objects. If you want to assign an object you created programmatically to an IBOutlet, just assign it.

<code>
IBOutlet NSView *myViewOutlet = myViewICreatedProgrammatically;
</code>

----

Oh well, i surely need to learn a lot. I'm going to write the app from 0 again in order to use a Controller class to handle both the plan and blocs, maybe it will makes things more clear both for you and me. *regarding the 'rude' word, i didn't mean you has been rude, but it's hard for me to realise how poor i am <sic>..* - StephaneDassieu

----

Stephane, 
while not obvious, because none of us sign our names, there are at least two and I suspect more, people trying to help you.
I'm the guy who was "rude".  the guy who responded to you was a different person.

I did not and do not take offense from what I read, nor mean any offense by anything that I write.

I only suggested reading more, because when I was in your shoes, reading cured my problems.  I used to think of Cocoa as Magic, things just worked, without explanation.  now it seems more like a library, ordered, well laid out, and predictable in the way it works.  

I certainly don't want you to stop asking questions, but from what I've seen in your posts, I know that the answers we are trying to give you are not helping as much as they should.  I think your english is very good, but the "language of cocoa" is what I think is holding you back.  Words that have specific meaning to a cocoa user, appear to hold no meaning for you. that can only come from reading and doing tiny projects or tutorials.

my experience started with hypercard, moved to a basic "Basic" on mac os 7.5, then to C and powerplant, and finally I am working in Cocoa and some applescript.  Cocoa is the very first OOP language/environment/framework I have used.  RealBasic is user friendly, and straight forward, and while realBasic != C, it does not exclude you from being able to use Cocoa.  A C course or two may help, as a lot of the "heavy lifting" in Cocoa defers to C  (Objective C is welded on top of C which means you can use C Inside OBjC), but its not necessary.  

Re: programatically instantiating an object and adding it to a view hierarchy and connecting its outlets.
1. you can and Should make a controller/view pair in IB which you can and should later load.  theres nothing that says you can't start out with an already setup view, and then move all of the connecting work so that it is between two controller classes.
2.  any control class (subclass of a view that allows mouse interaction) by definition allows you to set the target of its action message. (look in NSControl for exact phrasing)
3. NSView allows you to add a subview... any class that decends from NSView, supports this behavior.  so any object that basically takes up space in the window can have views inserted into it.
4. when in doubt take a good look at something that has already been done, that mimics what you are trying to accomplish.
Matrices, in this case.  they don't solve all of the same problems you are trying to solve, but Matrices will teach you alot.  examples of matrices, are: tableviews, browsers, outlineViews... all of them are control classes that display alot of different and ever changing data.  Not the way you intend to do it, but maybe close enough to help you get a head start.

----

*I already added     IBOutlet NSView *plan; to planView.h but what now?*

What you want is something like this:

    // blocView.h

@interface blocView {
    // ... alongside whatever else goes in here ...
    IBOutlet NSView *plan;
}

- (void)setPlan:(NSView *)plan;

@end


    // blocView.m
@implementation blocView

-(void)setPlan:(NSView *)p {
    plan = p;
}

-(void)onMouseClick { // or whatever
    [plan removeFromPlan:self];
}

@end


    // planView.h
@interface planView {
}

-(void)addNewBloc;
-(void)removeFromPlan:(blocView *)bloc

@end


    // planView.m
@implementation planView

-(void)addNewBloc {
    blocView *newBloc = blocView alloc] initWithFrame: ... ];
    [newBloc setPlan:self];
    // ... add newBloc to self, however that should be done
}
-(void)removeFromPlan:(blocView *)bloc {
    // Now we can remove the blocView bloc
}


I hope that helps you. As others have said, read more books! You'll find understanding the distinction between classes and objects, and the [[ModelViewController paradigm, very helpful.

----

Thanks to all of you, all your advices are really helping me. I've rewrote my app again and i'm using a class controller as a bridge between BlocView, PlanView and other controls.
Will try to complete my objective before crying victory but i think i've done the things the right way this time. - StephaneDassieu

---- 

Great, i did the code above and it's working great. As i used a Controller class to handle BlocView and PlanView, i modified it a bit (    IBOutlet NSView *plan; is     IBOutlet NSObject *controller now). I think my code is pretty neat now. Thanks again! - StephaneDassieu.

---- 

One thing you might want to consider is removing your custom PlanView and BlocView classes entirely, relying solely on existing View classes. Unless you're doing something **visually** impossible with existing classes, you should reuse the code already there.

*I'm sorry, i don't get you... Are you saying that i should try not to use custom classes (NSView subclasses)?*

Yes, that's exactly what I'm saying. Any behaviour that needs to be customised that's *not visual* should be moved to a Controller class. If you *can* use a standard class (i.e. any NSView subclass that comes with Cocoa), *do*.

The only time you should break that is when making a new, visual metaphor (say, a view that displays all its components in a rotatable circle, or maybe an Automator-like interface) that you wish to be reusable. In this case **only**, you should make **one** new NSView (that will contain and control the *visual* behaviour of its internal views) and *migrate* the Controller behaviour there. While you are initially designing it, **keep the code in a Controller**.

Chances are that unless you've actually used this metaphor more than once, you won't generalise it properly anyway, especially if you're a Cocoa newbie. Consider making an NSView subclass as the final step, only to be taken once the code is rock-solid and proven in multiple applications. Never do it prematurely.

---- 

Really disturbing news : almost all the tutorials i made used NSView subclasses.. even the Apple's ones!
Anyway, i already changed BlocView to a NSControl subclass, only PlanView is still a NSView subclass but it's not controlling the visual behaviour of it's internal views anymore, i made a Controller class. But i think's that's what you wanted to say is : i can make NSView subclasses but i have to handle them from a Controller (NSObject subclass) one right? Or should I prefer NSControl subclasses?? - StephaneDassieu

----

Okay, I confess, "never subclass NSView" is a little optimistic about just how customisable NSView is. But unless you know for sure what you're trying to do cannot be satisfied by an existing class, don't subclass! For example, why are you making PlanView? What methods do you need to put in it? Why are you subclassing NSControl? What methods do you need to put in it?

Are you sure you can't achieve what you want to achieve with a combination of existing Cocoa classes (no custom subclasses of anything other than NSObject!), actions, notifications, and delegates? If you're not sure what some of those latter meant, it's because you're missing knowledge of some of the best ways Cocoa provides for customising its classes. Check out CocoaGlossary for more.

It's possible that the tutorials are inadvertently pushing bad practices in an attempt to get working code in a short space. It's been a while since I've read one ^_^.

----
Ok, i'm totally lost now ahah... here my BlocControl.m code (I renamed BlocView to BlocControl as it is a NSControl subclass) :
    
// code removed - it was a really bad one and i don't want to give bad examples.


How could i handle all of this without subclassing NSControl and then 'Create Files' in IB?
And by the way, the code above is working great but when i build it, i got two warnings on     [controller removeTheBloc:self]; :

1. Cannot find methof '-removeTheBloc:'; return type assumed

and

2. 'NSObject' may not respond to 'removeTheBloc:'

Is it normal? - StephaneDassieu.

*to get rid of the warnings, declare     -removeTheBloc: in your header file. -->     - (void)removeTheBloc:(BlocControl *)removeBloc; already declared in the Controller.h Should i declare      -removeTheBloc: also in BlocControl.h ?*

----

Oops, i forgot to add     #import PlanController.h at the top of BlocControl.m
But i still have a warning : 'NSObject' may not respond to '-removeTheBloc:'

*Sounds like you've got a Controller object that's pointed to by a     NSObject*. Either change     NSObject to your controller type wherever its stored, or change it to     id (which will quash the warnings, but is less ideal).*

