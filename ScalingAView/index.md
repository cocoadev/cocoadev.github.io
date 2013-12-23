---
layout: page
title: ScalingAView
---



Can anyone tell me (or point me towards a tutorial on) how best to scale a view?

I've tried using     -scaleUnitSquareToSize: but it doesn't give the desired effect.  For one, it takes an integer that represents a percentage, so I can't really control the effect with a slider.  Also the scroll bars don't alter with the effect.

Any help will be appreciated.  Thanks!

----

You just need to change its bounds. See [http://www.mulle-kybernetik.com/artikel/MulleFaBVisualizer/]

----

Thanks - that works for scaling the view.  However, how do I handle the scroll bars?  Is there some way to update them or do I have to manipulate the clipView?

----

You could put the entire view heirarchy(view->clipView->scollView) in another view and scale that view, then everything, including the scrollbars aught to be scaled.

----

Tell the view you're scaling     [view setPostsBoundsChangedNotifications:YES]

----

Tried that - doesn't seem to work.  Should I put it in MyView's awakeFromNib or every time I change the bounds?  Tried both.  Are the scroll bars automatically registered to receive those notifications?

----

You don't usually deal with scroll bars directly in Cocoa. Your view is embedded in a NSScrollView. The source for MulleFaBVisualizer is available from the link above, so you could just look at that and see what it does.
----
I'll have another try at MulleFaBVisualizer, but I couldn't unstuff it on first go round.

*It's a .tgz file. Try GUITar on it if StuffIt is giving you problems -* [http://www.edenwaith.com/products/guitar/]

----

    -scaleUnitSquareToSize: will scale your view but it will not *enlarge* it. You need to change the view's frame for that.

----

Hey, does anyone know a workaround for scaling a view containing an NSImageView? The image view bounds seem to change, but the rendered image always looks the same size, and does not expand to fit the new bounds.

*I assume you're using something like     [imageView setImageScaling:NSImageScaleProportionally] and it isn't working?*

Yup. The image view itself is scaled alright, and I have tried NSImageScaleProportionally and NSImageScaleToFit. So when I "zoom in" real deep I have a giant image view, with a little teeny image at the bottom right corner. I think that it may be that scaling the image has to do with the frame, and not the bounds.

*Have you tried setting the NSImageView's autosizing?* 

    
[myImageView setAutoresizingMask:NSViewWidthSizable | NSViewHeightSizable]; /* Bitwise OR, not bitwise AND -- JonathanGrynspan */


Yes, but it's not the image view that is having sizing problems, it works fine. It is the image within the image view that does not scale right.

*Something definitely seems wrong with that picture*. ;-) I'm out of ideas. (* Pun wasn't intended but if I say it was, would you believe me?)*

How is the image getting into the image view?

----
I just want to add, that I noticed this a long time ago, and just ended up abandoning ImageView. I thought it was a problem with my old OSX 10.2.something and it would be fixed soon. What version of OSX are you using? -JeremyJurksztowicz

----
Of course, if nobody reports these bugs, they probably won't get fixed. Use the BugReporter!

