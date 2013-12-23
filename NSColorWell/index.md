---
layout: page
title: NSColorWell
---

Links:

http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSColorWell_Class/Reference/Reference.html

I spent about an hour and a half trying to figure out how to get the "opacity" sliders to display in my app.  It turns out that this line in my app's controller does it:

    NSColorPanel sharedColorPanel] setShowsAlpha:YES];

-shortstop

----


I had the same problem (actually it's how I found this site!).  There is another way:
    [NSColor setIgnoresAlpha:NO];

This appears to be an application global setting.  Not sure which method is preferred...

--Omegaman
----

I'm in your case, but I'm newbie in xcode dev and I don't know how to use these codes... Should I make a custom class for [[NSColorWell? or is there a place for that?
Thanks

ColorWellWeirdness

-eric
----
You will need to read a tutorial on Xcode for that info. It involves making what is called a Controller. The codes you see are in the Objective-C programming language. Go to CocoaBooks and CocoaTutorials. A good free book is called "Become an Xcoder" - it teaches you both Objective-C and Xcode. Also, the Xcode Documentation contains a tutorial on developing apps in Cocoa. Just open the Xcode help file and choose the Cocoa section of the documentation. You will learn how to make a controller, connect objects to the controller, and have actions performed via the controller. - Pietro Gagliardi http://web.mac.com/pietro10


----

Does anybody know what the little grey triangle that shows up in the top-right corner of color wells means?

*It means the color it's displaying was created in the Device space. Use the Calibrated space to make it go away.*

Excellent, thanks.

There is a bug with the alpha in 10.4: Changing the color sends colorChanged: to the first responder, but changing the alpha doesn't! 

----
How do I add a NSColorWell to a .nib file? - Pietro Gagliardi http://web.mac.com/pietro10

----
It's on the buttons and sliders palette, next to the button in the upper left.

----
Sorry - I meant NSColorPanel. I have NSColorWells already but I need a NSColorPanel to open up when I click one. I am working on an image editor to compete with Photoshop - with all the features!

----
NSColorWells do that automatically, I just verified this in IB using Test Interface.

----
How? No matter what I do, it always displays a tiny box with the color.

...

OK, I got it to work with the border. How do I do it **without** the border?

----
I think you'll have to subclass it.

