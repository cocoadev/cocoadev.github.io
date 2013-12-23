---
layout: page
title: HighlightButtonsLikeDLandAB
---

I'd like to create those custom popup buttons and action buttons that Delicious Library and Address Book use. You know, in that side panel - they highlight blue, which I think looks nicer than the Address Book style. How can I create a subclass that I can easily reuse of both nsbutton and nspopupbutton? Should I involve nsbuttoncell, or am I supposed to use those cell subclasses? I want to be able to set a regular button in IB or programmatically, and still use setTitle: on it. How can I do this? Where do I start? -- JasonTerhorst

BTW, here are some example screen shots to give you a rough idea:

http://www.jterhorst.com/popup_highlight.jpg

http://www.jterhorst.com/textboxsample.jpg

That textbox looks like something I'll want to try to do as well.

----

I did this in my first commercial application http://www.macupdate.com/info.php/id/18678 basically what I did was manage individual text *views* without the scroll view, draw the shadows below it, and relay everything as needed.

----

There's a video from WWDC 05 on ADC somewhere that show's how Apple did this. It involves using a NSTextView, getting the selection and drawing a shadow around it. They go through step-by-step. I don't necessarily like that method though, because you have to basically manage character ranges as text fields. It won't work at all for bindings. I always thought it might be more interesting to have a view that can draw the shadows around any cell that's key. Then implement the popup button as a cell as well. -justin

----

We originally went with this method, here is some advice, it isn't worth your time. You can get something that looks and acts 99% like a text field and do it much faster then trying to do that with something that wasn't designed for this.

*I'm not sure I understand you.... what should I use for the text field? A custom designed bit, or a text field?*

**This is a FAQ: CCDGrowingTextField**

What about the general look - that hovering effect with the blue?

*That you can figure out how to do with NSBezierPath, NSActionCell, some mouse event handling, and a few other very common things. You'll want to create your own custom control, basically. On mouse entered, draw the control with the blue half-round-rect and white down arrow. On mouse out, draw the regular label.*

----

What I use is one main view that manages a number of subviews, most of which are of class NSTextView.

I add those subviews and remove them as nessesary while maintaining a cache of them to reduce the number of allocs etc as the values change.

These text views are not maintained in a NSScrollView, I manually query what their size is and adjust the origin of all the others as needed.

I know that Wil Shipley is anti-images (and would rather do all drawing in code), but what do you guys think? Too slow? Should it all be done with source code?

*If it's a very complicated view, why shouldn't you use images? Wil Shipley bedamned ... ;-) Anyway, I use images for ultra pretty controls that "can't" be done in code. By "can't", I mean something that would take months of research for the average coder to get something that's still ugly compared to a quick Photoshop job. Personally, I think Mr. Shipley didn't think his theory through. Take creating a 'bar' for instance. If I can create a one-pixel-wide shiny high-gloss gradient and stretch it (in drawRect) across a view in two lines of code (one for the size, one for the drawing), versus several hundred lines minimum to achieve the same effect, why the hell shouldn't I use an image? Even if you draw the 1-pixel-wide gradient line in code, cache it, then use that cached image from that point forward to save time, you're still back to that same choice: all-in-code versus cached image. Which do *you* think is going to take less execution time in that case?*

You're right. The only reason I asked is because his reasoning, in one of his blog posts, was that when Liger, or whatever the heck it will be, comes out, it may allow scaling of objects in the visual field, which would blow up anything and everything on screen. If our controls are made with Photoshop, and look nice now, will they still look good at 500x on an Apple 20-foot Cinema display (or something like it), when the OS is scaling everything on the screen?

*And without those pretty controls, we're reduce to using stock controls or drawing things that look like the Windows interface because small-time developers have little recourse. I know *I* can't spend months figuring out how to do some of the (rather nice) custom controls I've got in my apps in all vector graphics. Sure, Core Image makes it easier to do a lot of stuff, but only if you take the (high investment in) time to learn it and graphics concepts in general. Feh! I don't have the time or patience. If Liger has a much more detailed 'kit' (like CoreUI or something) that lets us easily apply pretty shiny gradients to any shape and many many other important custom control effects, more small-time developers would adopt the practice - I know I would try.*

The differences from the point of an end user shouldn't be underestimated - if I want my system highlight colour to be bright pink then your app should conform to that. Apple don't all the time, but I think they should. Less code is cool, but not when it detracts from functionality.

I think you've completely missed why it's better to do simple drawing in code.  First, it's scalable.  If you want bigger stuff, you can simply draw it bigger.  No need to create another graphic.  Also, it's better for pdf files if a view gets printed.  Second, it's reusable.  There are categories to do the rounded rect thing easily and having a gradient drawing algorithm is often handy.  Third, and most importantly, it's one less thing to worry about.  Once the code works, it works.  No extra graphic file to keep up with or to worry about having it get into the build phase and whatnot.  And I should also remark that you've grossly exaggerated the amount of work implementing any of this is.  It is *not* "several hundred lines minimum."  There are plenty of examples and source readily available for someone to grab and stick into a project, so your work is reduced to copy-and-paste.  As for execution time, I'm skeptical that scaling a pre-built graphic is much (if in any way) faster than simple drawing.  Simple drawing like what is presented here is best done in code.

*Can you point me, then, to an example of how to draw Tiger's menu bar gradient in code as an example? If it's *that* easy, I surrender unconditionally.*

**Evidently not ...**


Shouldn't you all focus on getting the layout and toggling of editing to work, and _then_ make the fields pretty?  *Nobody suggested otherwise, so I'm not sure why this comment is necessary.*

----

If the drawing code is much more complex than the image, then use the image, by all means. I would never advocate adding hundreds of lines to your program; I hate code. I just said I'm starting to lean away from static images and towards doing them in code, for many of the reasons specified above by others. -Wil

----

What's the name of the typeface that's used for the controls in the above screenshots? Is that a typical OS X font?

*Isn't it the control content font?*

    
NSFont * theFontTheGuyWasTalkingAbout = [NSFont controlContentFontOfSize:
                     [NSFont systemFontSizeForControlSize:NSRegularControlSize]];


It's Helvetica.

----

So, to the guy that was saying that it would be easy to draw the popup in code -- how do you do it? ... in the NSPopUpButton subclass, or in another part of it? The textbox wouldn't be too hard, but the question about how to have it draw the actual prettyness of it, so that it can still use the usual methods, but with the style (and helvetica, noted above). In the meantime, I'll try the image method, so that maybe we can benchmark both of these, and settle the argument once and for all.

----

I have posted a follow-up for drawing text boxes like the one above at BorderedShadowedGrowingTextField.

