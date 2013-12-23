---
layout: page
title: QuartzCompositionInCocoaFullScreen
---



The application I'm working on has a quartz composition as its main view.  I have a button in my NSToolbar that I would like to in turn make my quartz composition go full screen and appropriately be able to exit when escape is hit.  I didn't think this would be much trouble but having looked at half a dozen tutorials or so I'm stuck.

Anyone have a solution or a tip?

Thanks a lot!

----

HowToAskQuestions

This kind of extremely general question is not likely to get a helpful response, which is your goal. Help us help you by providing more information about what you've tried and asking more specific questions about the problems you've had.

----

I'll try to help you out, but there are a million different ways that Quartz Composer can be rendered to a view. Are you using the IBPalette that is in the Dev folder, or are you using the OpenGL method of rendering? It's easier to work with the palette, but doesn't have the advanced options that the openGl method has. -- JasonTerhorst

----

I'm using the IBPalette.  I dragged over a view and imported a Quartz Composition.  It works fine and I've got the Quartz framework and it works on multiple machines.  What I need to do now with it is launch it full screen when I click a button in my NSToolbar (goFullScreen:).

----

Okay, well that could work, but you have to get rid of your title bar and toolbar, and make the titlebar-less window go to the size of the main screen.
You should look at the documentation for NSWindow to find out how to get rid of the title bar and toolbar (hint: you might have to create a new window, and copy the contents of the existing window). Then, set the level of the window to be higher than all of the other windows on the screen (using the NSWindow setLevel: method), and make the window full-screen (using the setFrame:display: method, with [[NSScreen mainScreen] frame]). It may be simpler than other ways of doing this, but I think the OpenGL way gives you more options, and OpenGL can actually keep other apps from jumping in your app's view. But, that, my friend, cannot be as easily done, but the information a book (like Aaron Hillegass' book, or a book on OpenGL) can provide will be quite helpful. -- JasonTerhorst

----

Searching the site for "F<nowiki/>ullScreen" turns up many useful pages. Try ImplementingFullScreen or one of the others.

