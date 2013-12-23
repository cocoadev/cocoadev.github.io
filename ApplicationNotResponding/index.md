---
layout: page
title: ApplicationNotResponding
---

Hey guys,
I am having a wierd problem with my app. I have an NSView with an MUPhotoview (thanks Blake Seely) on it. At startup I add the NSView to a NSTabView because there are many views to handle. Everything works fine until i subview the MUPhotoview into an NSScrollerView in the IB. The wierdness I am having is that the app is not responding at all. It launches and I see the main window, but I cannot click on anything. It just sits there. What could cause this? Thanks for the help.

----

See HowToAskQuestions, PostYourCode

----

You can download the MUPhotoView code from http://blakeseely.com. I cant show my code because there is nothing to show. I want to know what would cause an application to not respond if you put a view into an nsscrollerview.

----
Use the debugger, use Sampler, do some investigation on your own to determine what is going on.

----
I am using the debugger. I dont know how to use the sampler. Any good places to learn? I don't understand how an nsscrollview can affect an app like that. It doenst make any sense. The MUPhotoview is totally fine and the app works until i put the MUPhotoview within an NSScrollView. Any ideas?

----
Use the debugger, pause the program, whats it doing? Repeat... Meanwhile at least _try_ sampler - or surf for some instructions.... 
----
LOL.. I know how to use the debugger. Using the sampler application doesnt show me anything after attaching. Can anyone answer why an NSView subviewed in an NSScrollView can make the interface non-responsive? There is no beach ball. I just cant select the main window nor the menu. It's like it is inactive. If I were to select the Finder and come back I get nothing. The menus doesnt even show up. Is that an NSRunloop problem? can the scrollview interfere with what the app needs to do in order to start up? NSApp's delegate methods dont even get called anymore such as applicationDidFinishLaunching. So obviously something is interfering with something needed to finish the app launching. What could do that?

----
The MUPhotoView example shows it working in a NSScrollView, therefore you must be doing something wrong.  So we can't help you unless you show us your nib/code! Meanwhile if there is no code as you say then why not just recreate the project.

