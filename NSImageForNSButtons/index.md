---
layout: page
title: NSImageForNSButtons
---



Thanks to CocoaDev, i know how to dynamicaly change the image and alternate image of a button.
But i've seen that some app use an unique image (with several tiny drawings) for several buttons.
I've tried to play with the NSImage but it's a real challenge to me.
In fact i tried to modify some code i found on ThumbnailImages discussion without success.

Let's say my image is 80*80 with 4*20 pixels icons (4 on x and 4 on y)
How can i 'extract' the x=3, y=4 ico,n for example? - StephaneDassieu

----

Are you referring to NSSegmentedControl? That may do what you want. Also, the large buttons at the bottom if iMovie (that select what palette is displayed) are just normal buttons directly side-by-side that use images meant to be ... side-by-side. ;-) Like "Left button cap, middle button, right button cap".

----

No, my problem do not comes from the NSButtons. I have 4 buttons at different places on several windows. Each button can have 4 states/icon : mousewithin, mouseout, mousedown and disabled. I plan to deal the mouseout/mousedown with NSButton's setImage: and setAlternateImage: methods and the mousewithin with the NSView methods of the buttons.

The problem is : i have only one image containing the 4 states of 4 buttons. The icons are only (let's say) 20*20 pixels so the whole image of 4 buttons and 4 states is  80*80.

So my question is how can i tell -for example- button #2 to set its image to the NSRect { {20,0}, {20,20} } of my image, its alternate state to the NSRect { {20,20}, {20,20} } and etc.. ?

----

Well, why not just have four images instead of one?

Alternatively, create a new NSImage with a size of 20x20, lock focus on that image, and �draw from rect� using your master image. Then set the new (smaller) image for the button.

----

I know it sounds like i'm getting thing more complicate than i should but in fact, the image with the icon i'm trying to use is extracted from the content of an Apple app. So i just want to learn how to do it. I'm gonna try (again) the "draw from rect" thing.

**UPDATE :**

Just found the solution by myself (you are really too slow guys! *kidding*)

In fact i just didn't get how NSImage where working (it's really not very obvious), here's my code :

    
// Creating the button
closeBox = [[NSButton alloc] initWithFrame:closeBoxBounds];
[closeBox setButtonType: NSMomentaryChangeButton];
[closeBox setBordered: NO];

//Creating the images
NSImage *wholeImage = [NSImage imageNamed: @"automator-buttons"];
NSImage *closeImage = [[NSImage alloc] initWithSize:NSMakeSize(20, 20)];

//Croping wholeImage into closeImage
[closeImage lockFocus]; // I'm going to draw in this image
[wholeImage compositeToPoint:NSZeroPoint fromRect:NSMakeRect(20, 40, 20, 20) operation:NSCompositeCopy];
// (20,40 means second insight image on x and third on y)
[closeImage unlockFocus]; // I'm done drawing in it


That's quite simple once you get the 'lockFocus' principle. - StephaneDassieu

----

**Not slow at all - we're telling you the correct way to do this. ;-) While it's good to learn how to compose images on the fly, this is an incredibly inefficient way of changing the state of a button. If the four states are always going to look the same, it's infinitely more efficient (translated as "won't make your application needlessly dog-slow") to use your favorite image editor to pre-render four static images. If, however, your button has a changing state (where some information is graphically represented within the button itself) you seriously need to rethink your design as this is rather un-Cocoa-like. Other than that, happy learning. ;-)**

----

You are probably right, but as i said above, i took this image from an Apple application... So if it is so 'dog-slow' why do Apple do it?

**Maybe there is a misunderstanding. You cut up the images at the beginning of the app (or module) and then use and reuse them later, right? I think dog-slow was refering to cutting the images each time the button state changes.**

*They don't. You're misunderstanding their approach.*

**Could you maybe be less vague and tell us in exactly what application and where in that application we can find the button you're talking about? That'd be infinitely more helpful than debating vague references. ;-)**

I'm speaking about Tiger's Automator but for the non ADC people, wait until the 29 to see the pic i'm talking about ;-)

