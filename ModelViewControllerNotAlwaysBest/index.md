---
layout: page
title: ModelViewControllerNotAlwaysBest
---

Perhaps this would be a good place for a discussion of Squeak's Morph design pattern.

----

And sometimes, as in games, the view is inseparable from the model, as the following discussion hints at:

----

I have written a first version of a fractal generator in Cocoa.

I began a discussion in a related thread ( NeedOpinionOnBestGameGfxView ) where I discuss my intention to write a simple game with keyboard controls using OpenGL.  I think I may use CoreGraphics afterall - not sure at this point.  Anyway, I want to be a good Cocoa MVC developer, but have a need for some opinions on the strucutre of the app.

The is a port of an Apple II game called Sabotage ( see screenshot in the abovelinked thread ).  The user will control a turrett with the keyboard that aims and fires a good number of bullets at descending paratroopers dropped from scrolling helicopters.  That's about it.  My question is where to put what code?

There will be a custom view subclass tied to the NSView (or NSOpenGLView) and a controller, at least.  Is the meat of the code that controls the app - that tells the copters to scroll, that tells the turret where to point, that starts the bullets moving, etc.  all to reside in the Controller?  Or is that more suited to the view subclass since its graphics ops?  

Should the Controller be a subclass of NSWindow or NSObject?  I've seen both in example code.  How about a specific item like, where is keyDown  to be located?  In the view subclass or the controller?  Just some general thoughts on the division would be great.  Thanks.  Definitely will still be learning thru this example, which is part of the point.  


blakespot

----

I don't think MVC can be applied very well to games. They are so view oriented that it is hard to separate the logic from the view (collision detection for example). Instead, I would focus on separating the objects in the game: You could have a helicopter class, a turret class, a bullet class, a paratrooper class, etc. Where each class handles it's own animation, drawing and key down events - probably only one object will be handling the key down events in this case so there may be a better way to manage that. An controller object higher up will have to handle the collision detection because objects don't know about each other. I'd be interested in what others have to say who may have more experience in this area.

*Should the Controller be a subclass of NSWindow or NSObject? I've seen both in example code.*

Umm, I don't understand why it would be a subclass of NSWindow - it shouldn't have anything to do with windows or even the AppKit. A subclass of NSObject should work fine.

*How about a specific item like, where is keyDown to be located? In the view subclass or the controller?*

I would put it in the view subclass for now and forward it directly to a controller. Later on you may need to grab the events in a different place - such as in a NSApplication subclass or in a custom run loop - but either way you could just forward them to some kind of input manager. It keeps the system flexible.

-- RyanBates

----

"Should the Controller be a subclass of NSWindow or NSObject?"

I think you meant to ask if the controller should be a subclass of NSWindowController or NSObject. I don't know the answer to that question.

*Oh, in that case you would want to ask "What is it controlling?" If it is controlling the window and its contents then you may want to make it a subclass of NSWindowController; but, considering that it is a game, you probably will not need to control the window or the view subclass. If it is controlling the game itself, then I would go with an NSObject subclass. --RyanBates*

