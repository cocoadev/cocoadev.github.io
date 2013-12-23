---
layout: page
title: LoadViewFromNibIntoSplitview
---



I've got a pretty common interface (like Mail, iTunes, etc) with the basic dual splitter. (Folder List on left, then another NSSplitView on right with contents of folder at top right and object inspector on lower right).

My mainmenu.nib has a simple window with three NSView placeholders (with their outlets saved in the controller's variables).  Because of the challenge of defining all my controls inside multiple splitviews, I've decided to have each "subview" of the overall window to be stored in separate NIBs and loaded at runtime.  This makes maintenance easier and also allows me to dynamically add additional object inspectors in the future.

Here's the scenario/issue(s):

1. I load the custom NIB just fine.  I then add the custom view from the nib as the subview of the placeholder view in the mainmenu.nib.  (e.g. [folderView addSubview:folderSubView];)

2. Question: what is the correct time to perform this code?  AwakeFromNib in the Main window?  Or some better time?

3. Once the windows appear, they are the wrong size and resizing the splitter sure doesn't help.

4. The views basically show up, but they are not oriented to the correct "starting corner", (e.g. their starting origin) is all wrong.


OK, so I'm a newbie at all this.  But I want to structure the app better the first time instead of just building a big "mainmenu.nib" with all of the controls slammed in it.  Here's an image of the parent view and the subview that will appear on the left.  

http://www.digitalintegrity.com/images/samplewindow.jpg
http://www.digitalintegrity.com/images/subview.jpg

I'm assuming I need to do a lot of custom resize logic.  I don't want to use something like RBSplitView. 

----
I don't think you have to do any custom resize logic at all.
Set the springs and struts for you custom views correctly in Interface Builder.
When you place you custom views in the splitview (either replacing each placeholder view or as a sub-views of the placeholder views) just set the frame of each custom view to the frame of the view being replaced (or set the frame to the bounds of the place holder view if you are adding a sub-view).

----
OK, that's useful, thanks,  but it creates two questions:

1. How do I "replace the placeholder view"?

2. What is the right time to set the frame size (or invoke the code to replace the views) ?


I think what I'm trying to do is pretty common.  Many apps that I've looked at (by examining their packages) seem to use a scenario like this.

----

1.     
[someNewView setFrame:[placeholderView frame]];
placeholderView superview] replaceSubview:placeholderView  with:someNewView];


http://developer.apple.com/documentation/Cocoa/Conceptual/[[CocoaViewsGuide/WorkingWithAViewHierarchy/chapter_4_section_5.html
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSView_Class/Reference/Reference.html

2. Perhaps I don't understand the question.  Do it when it makes sense for your application.  The only time you can't change the view hierarchy is within a hand full of methods like -drawRect: that depend on the hierarchy.  -awakeFromNib is a possibility.  In a NSDocument based application, - (void)windowControllerDidLoadNib:(NSWindowController *)windowController is an option.  You might do it in response to a user action.

