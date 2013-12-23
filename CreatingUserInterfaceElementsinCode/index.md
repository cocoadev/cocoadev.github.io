---
layout: page
title: CreatingUserInterfaceElementsinCode
---

I have some code I am using to create a new testView (which is an NSView subclass created through project builder):

    
testView *myView=testView alloc] retain];
[[NSRect mainRect = [mainView bounds];
NSRect newRect=NSMakeRect( mainRect.origin.x+((mainView subviews] count])*150) ,  mainRect.origin.y,148,68);     

[[NSPopUpButton *locationButton=[[NSPopUpButton alloc] initWithFrame:NSMakeRect( 0, 0,108,30)  pullsDown:NO];

[locationButton addItemWithTitle:@"Toronto, Canada"];
[locationButton addItemWithTitle:@"London, England"];
[locationButton addItemWithTitle:@"Tokyo, Japan"];  

[myView initWithFrame: newRect];
 
[myView addSubview: locationButton];

[mainView addSubview:myView];


mainView is the main view that I add the myView to. Basically I want to be able to add a new myView to the mainView at the click of a button and each myView can be customized through user interface elements so it can display something (ie weather.... you create a block, select Toronto and it shows the weather for toronto, then you can create another block (myview) and specify another city etc).


I have a couple of questions.

1. How can I get the locationButton to send an event when a new item is selected, also how can I get and set the value of the selection?

2. As I stated I do have a custom class but if I put the locationButton creation code in the init method of my testView custom class then when I create a new myview, it creates a new view but the popupmenu is added to the first view created rather than the one it is supposed to. How can I make it so I initiate a new testView object for each block i create?

Thanks,
Tommy

----

You can get your NSPopUpButton to execute an action on some kind of user interaction by setting its target and action. For example:

    
[ locationButton setTarget:self ];
[ locationButton setAction:@selector(changeLocation:) ];


This will cause your NSPopUpButton to call changeLocation in the current object when the user clicks on the pop up menu. The call will be made whether the user changes the selection, or simply re-clicks the option that is already selected.

Not sure I quite understand your second problem. Post some more code if you would like someone to take a look at it.

~ TheMZA

----


* you do not need to retain something created by +alloc/-init or by +new.
* you need to elaborate further on your second question.


*--boredzo*

