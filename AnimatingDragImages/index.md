---
layout: page
title: AnimatingDragImages
---

Short version: I'd like to animate a drag image mid-DnD. I've tried simply modifying the dragImage of the draggingInfo, but to no avail. Ideas?

Long version: Hey, all. I and a few others have recently finished adding a brand new backgrounds configurator to Pixen (http://www.opensword.org/pixen). Here's a screenshot (still somewhat work in progress):

http://www.opensword.org/images/backgrounds.jpg

Anyway, I feel that it's not entirely clear that the rows from the stacked view on the left are to be dragged into the lozenge things at the right, so I'd like to do a Dashboard-style transform from the drag image of the template previews to the drag image of the full-out background configurators on the right when the drag location leaves the stack view. Now, generating the frames of that image is really no problem. I can just interpolate the scale and dissolve between the images. but the issue is that I can't figure out how to modify the drag image of a drag mid-operation. I've tried simply modifying the draggingImage one can get from the draggingInfo, but modifications to it aren't reflected in what's drawn to the screen. Does anyone have any ideas?

(ps: some of the many custom views used in this configurator might be useful to someone; the source is up for grabs on our SVN repository if anyone would like it. details at http://opensword.org/pixen/download.php)

-- AndyMatuschak

*Check out the NSDraggingSource informal protocol; it looks like it has the exact method you need. Just implement     draggedImage:movedTo: on the source, and change the image...that should work, unless the image still isn't being updated. --JediKnil*

That method is only called on mouseMoved, so I wouldn't be able to animate a change. Additionally, modifying the image (I tested this by locking focus on it and filling with red) doesn't seem to actually reflect on the drawn drag image. -- AndyMatuschak

Try making a borderless window that follows the mouse. Set the drag image to a small clear rect and use the window to do the animation. - Guy English

That's not a bad idea. I'll give that a shot once I get a change. Someone else suggested that I figure out (through window lists, I guess) the window that Cocoa's using to display the drag image and modify "that". Which seems tremendously hacky but perhaps simpler. -- AndyMatuschak

Oooh - don't do that! :) You can't know how the drag image rendering is implemented - it may be a window for larger size drags or they may modify the mouse cursor temporarilly if the image is a certain size, or they may have the window server composite the image in directly without using windows. Or they may just use windows now but when they optimize or change it in someway your code will die horribly. At least before you do anything kooky have a look at the Carbon Drag manager - there may be some method in there to maniuplate the image during drag. - Guy

Any progress made on this front? Looks like the new Interface Builder is implementing the same thing now...

I happened upon this page mere seconds after visiting this other page: http://joshua.nozzi.name/source/jlndrageffectmanager  which appears to implement exactly what you're looking for.  -- RussellFinn

----

Not only that, but the Carbon Drag manager mentioned above has been all but completely deprecated (due to the move to 64-bit) in Leopard. The window idea seems like the most plausible solution at the moment, unless someone else has a better idea.

----

