---
layout: page
title: ProblemLoadingNewNIB
---

What happens to the application after it launches? Which methods are called? 

What method is called after the nib is loaded, so I will know which method to overwrite/delegate?

ex : the window doing some init in ->     (id)init method

What methods are invoked after that?

- JuliusJuarsa

----

There is a CocoaIntroduction page at this site. Although your questions seem at first glance more specific, and your other posts indicate that you have some sophistication, you may need to study some of the topics listed there, especially if you want to understand the use of the     init method.  Of course it is very confusing at the beginning. Start with     awakeFromNib which will be invoked after the nib file is loaded as your application launches.

You have a lot of control over what happens to your app when it launches or when a new window opens. Study the documentation for methods like     awakeFromNib and delegate methods like     windowDidLoad or     applicationDidLoadNib. The various delegate methods will give you more delicate control. But, to be perfectly honest, you will be better off if you do this reading yourself.

I also suggest that you do a search on this site using the terms "delegate" or "startup" and read some of those (and then use what you learn to search beyond this site). CocoaDevCentral has some good articles on this subject as well.

----

I created an application that loads a nib file and then displays the contents in a window.
I have 2 nibs - one is for the main menu and the other is the template for displaying the data I read from file. When I called the second nib from the first nib it didn't show anything.

Here is the script I used to load the nib :

    
    [NSBundle loadNibNamed:@"objViewer" owner:self]; // the nib name is objViewer.nib


For the second nib, I created a subclass of NSOpenGLView and just put a few lines to test whether it's working or not:

    
// the glView.m

- (void) awakeFromNib
{
	NSLog(@"nib is awake");
}


- JuliusJuarsa

----

Learn how to search this site for discussions related to your issue. This case, a search for     loadNibNamed will turn up several interesting pages.

I might suggest you're trying to sort out too many things at once. Forget about the NSOpenGLView for awhile - there's plenty of sample code out there if you want to learn how to use it later. Do a separate project to test the loading of a second nib: create a controller class with a new action in your main nib and connect it to a menu item. Put the message to NSBundle there. Then, in the second nib, add a second test class and put your log statement in the awakeFromNib method in that class to show that the second nib has been loaded. A BOOL value is returned by the message to NSBundle; learn how to use it. Put some tests in around the loading of the second nib, so that the app does not load that nib again once it has been successfully loaded the first time. To do all this, you may need to learn about the use of FilesOwner, which is the object that loads a nib file. The FilesOwner in the second nib can be a proxy for the test class there, or you can make a separate instance of your test class in that nib file, but FilesOwner saves you from having to do that in this case.

When you have a working example, read ProblemWithLoadNibNamed, AvoidAwakeFromNib, or LaunchANewWindowProgramatically for further insight.

After you have that sorted out, then go on to worrying about how to control your view subclass. There is a rich literature on the subject of view subclasses, both here and in many other online tutorials. Try to do some reading, and **first** (please!!) work through a few examples where the code is given to you along with the steps for putting it together. Read the LearnOpenGL page here for links to Cocoa GL tutorials.

----

I searched the forum as you suggested, did some testing and here is what I learned so far (correct me if I'm wrong):
1. when you load the new nib with [NSBundle loadNibName:owner:] method, make sure your new nib window property "visible at run time" is checked
2. before calling the new nib make sure to "alloc and init" the class controller then call the new nib

My question now: Is it necessary for each window to have its own controller? In my case, I need the subclass of NSOpenGLView. Can I just put the subclass of NSOpenGLView as the controller and the view into one class?

My aim is that MainMenu.nib will be the main menu and the new nib will be the template to draw the object, so I need to load the new nib. - JuliusJuarsa

----

It is not necessary to give each window its own controller. Under some circumstances, it is to your advantage to do so. Your points numbered (1) and (2) still indicate to me a very piecemeal understanding of the use of nib files. For example "calling the new nib" does not make any sense. You can instantiate objects via a nib file, or you can do it programmatically, but you need to do some more reading to understand when you might want to choose one over the other.

If you want to rush into production, do so with my blessing. If you want to learn about how Cocoa applications work, by all means stop what you're doing and work through a few examples. I would not under any circumstances recommend that you use an NSView subclass as a controller. You could try studying the material associated with ModelViewController, but I am beginning to suspect you are in too much of a hurry to do so. You very much need to develop a better understanding of how NSView and its subclasses function in the Cocoa frameworks.

What I really wish is that somebody much more knowledgeable than I am will come to the rescue, but it is so difficult to determine what it is you want to do, that I doubt any such help will be forthcoming. To repeat, please study some examples to see how Cocoa applications work on a basic level before proceeding with your investigation. You appear to know enough about programming in other environments to benefit from such study.

----

Thanks for the reply.  I have some background in programming (mostly borland delphi, some C, some C++, php, pl/pssql). The Cocoa concepts of programming are so much different from my usual development tools. Maybe I started on the wrong foot here. I started to learn cocoa by reading the BookCocoaInANutshell and James Duncan Davidson, but it confused me. Then I tried reading BookBuildingCocoaApplications, but the problem of this book is that it doesn't tell me in detail what this for. Instead the book just tells me to do this and that, so I can't grasp the Cocoa concepts better. It got me get going (but still with little understanding). I found out about this site just a few weeks ago and it helps me a lot. So for a newbie like me, what do you suggest? Where do I start from scratch? (I rushed into production due to the deadline on my final assignment.)

----

BookCocoaInANutshell is more like a bound, brief copy of documentation for someone's idea of the most useful Cocoa classes. BookBuildingCocoaApplications just shows the development of several applications, using the concepts that you need for your project, but doesn't fully explain the basis of those concepts; it is terribly bloated with a lot of extraneous material not really of interest at your stage of the game. BookCocoaProgMacOSX by Aaron Hillegass is probably the best introduction to the subject for someone with your level of programming knowledge but starting from scratch in Cocoa. Please also see the many fine tutorials at CocoaDevCentral. Hope that helps.

----

Thanks for the tip, let me dig into BookCocoaProgMacOSX first, then go on to some serious programming  - JuliusJuarsa

