---
layout: page
title: VariousQuestions
---

Allright... I could use a little bit of help here.  For the last month or two, I've been studying cocoa, and have gotten fairly good at it.  The only thing is, I don't feel like I'm creating anything.  I feel like I'm just using the NS classes, and not doing much on my own.  All my own classes are just ones which use NS something-or-another.  How can someone create completely new things, such as a completely new or custom button from scratch?  I want to have a thorough understanding of how this stuff works, not just that I can say [myTextField setStringValue:@"Hi"];... I want to know what's going on as it sets it to "hi".  I want to know how to make something like the General/NSTextField.  I think you guys can see what I'm getting at.  I want to go from someone who uses these things to someone who creates them.  Unfortunately, I don't even know how drivers work, and I learned C for two days before starting on Cocoa.  I know how to make ints and so on, but I don't know enough about C... or at least I don't think I do.

I recently made a calculator from scratch, and a little program to manage "records"... just to see if I could.  Still, I don't know enough about how to make customviews which are actual controls, and not just crazy lines and shapes.

Thanks a lot.

-Charlie
----
One of the beauties of Cocoa is that you don't have to make something like General/NSTextField from scratch.  If you wanted to create something similar to it, you would just subclass it and go from there.  I am pretty new to Cocoa too, and one of the first things I did was to subclass General/NSButton and make it bounce around the screen.  I was just doing this to play.

I would say just to start by expanding the functionality of a current control, for instance make one of those buttons that when you mouse-over it, it moves away, so it's hard to click on.

To actually make a button from scratch, you would have to draw one (which I am not too familiar with actual drawing) and add code so when you click it, it would do something.  It wouldnt' be TOO hard, but in actual functionality, most programs you would write would hopefully use the standard, functional, bug-free buttons that already exist.

Also if Apple decides to update the API, any classes you created by subclassing, would automatically get updated too.

--General/DerekCramer
----
Thanks a lot for the response.  What I want to do is also a 'just for fun' type of thing... but curiosity: how'd you make it bounce around the screen?  Is what you did have it sense where it is, and then if it's too close to the edge of the window, move it?

Which reminds me: How on earth do you make a delegate method?

thanks again
-Charlie
----
To make a delegate method, you don't really do anything special. You just need an id type class variable that will point to the class's set delegate, methods to set and get the delegate, and then in the approriate areas call the delegate methods with the delegate class variable as the target. If it isn't set (i.e., nil) it won't do anything. Also, to be super safe (like if you're making optional delegate methods that are going to be used by people other than you) you'll want to check and make sure that the delegate responds to the delegate methods before you call them. --General/KevinPerry
----
To make it bounce, I have an outlet to the General/NSView, and use a General/NSTimer, move it a bit every tick and check if it is going off of the screen.  I even have a gravity thing happening, works pretty good, I am just working on having them bounce off of each other now.
--General/DerekCramer
