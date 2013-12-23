---
layout: page
title: AppKitMostWanted
---

**Guidelines** 

You'll keep this page easy to read and update for everyone by using the following guidelines:


*When adding a comment, leave a blank line between your entry and the previous one.
*If the previous comment is in plain text, make yours *italics* by enclosing it in double apostrophes (and vice versa). This makes it easier to tell the difference between two comments and a single, multi-paragraph comment. 
*When adding a new item, copy the following (while in edit mode) and use it as a template:

    

***yyyy.mm.dd: name or subject** -- description
----
**Vote: AYES 1, NAYS 1**

**Comments:**

----


----
----


* **2004.04.29: Easier DragAndDrop for NSTableView** -- I've spent the past hour trying to get my table view to accept file drops, and I already knew (mostly) how to do it (DragAndDropTableView helped as well). This is too d�mn hard... It should be a setting in IB.
----
**Vote: AYES 4, NAYS 0**

**Comments:**

Agreed 100%.  You have to register for the fricken drag type, there's no nice interface for catching modifier keys, and writing the fricken data should have some fricken default protocol adherence (e.g. just use NSCoding) and then if you want a deep copy, a pointer, or what you can override something.  I like that accept, validate, and write methods that are there, because occasionally I really need something that's not standard, but jeeze louise, not all the time.
----

* **2004.09.28: NSColor and classes using set, setFill, setStroke and drawSwatchInRect:** -- There's undoubtedly some history behind why it is the way it is, but NSColor and classes like NSView seem very poorly designed (in OO terms) in that applying a method in one object affects the behavior in another even though the two objects are not explicitly related (e.g., you set an NSColor to affect the color used in an NSView even though the NSColor object doesn't belong to the NSView object). Maybe what's more central to the issue here is that "drawing" behavior isn't intrinsic to the abstract idea of color and therefore doesn't belong in NSColor. Some classes, in fact, do handle at least some colors more appropriately (e.g., NSWindow has a setBackgroundColor method) treating the color as an attribute of the object it is applied to. 

For classes like NSView, the current implementation of color also allows for something that is wrong (or at least nonsensical), namely that you can use colors defined in terms of different color spaces within the same "drawing." It is more correct that a "renderable" object has a single color space through which all colors must be defined. While it's true that a color can't be independent from its color space, binding the "create color" methods to the "with color space" directive (e.g., colorWithCalibratedHue:saturation:brightness:alpha:) is perhaps the wrong way to implement things. Instead, creating a color space object and then creating colors within that color space would get the "color space-to-color instance" relationship right and renderable objects could have a single color space attribute through which its colors are defined (and can only be defined).
----
**Vote: AYES 1, NAYS 0**

**Comments:**
Uh, I think the reason behind the method -set exists in NSColor is in Quartz or Display PDF model (or, DPS from the NeXT days.)  The PDF renderer is a state machine, and you're modifying the state using [NSColor -set].  I agree that it apparently violates OO idealism, but the true receiver of the message is the current graphics context.  Thus it is natural to use [NSColor -set] in drawRect:, as you're directly touching the PDF context on behalf of the NSView.  Speaking pure OO, you should also abhor global existence of autorelease pools ! (Apr12, 2005)

*I agree it's not very OO, but the technical reasons are as stated above. However, it's very easy to create your own objects which do handle this detail for you. For example just subclass NSView and add e.g. fill and stroke setter methods which are applied when the view is focused. In practice you need much more granularity than this, so there isn't a general solution that works for all possible situations. I think that's why it's been left at a relatively low level - it gives you the flexibility to do what you want. Solutions for some classes of app - vector drawing say - can be generalised to a greater extent and in that case you'll probably find that the stroke/fill of objects is highly object-oriented. (October 17 2006)*

----

***2004.09.30: NSSlider (continued support for background images)** -- The methods/attributes to support background images on sliders (like those found in the Color Panel) have all been deprecated (more accurately, *discontinued* as there are no replacement methods/attributes). In general, I find these kinds of changes by Apple troubling. On the one hand, they preach that consistent UI's are a critical element of a good user experience but then they disable a feature that they, themselves, are using. 
----
**Vote: AYES 1, NAYS 1**

**Comments:**

While on the NSSlider topic, another issue is that the control is sending out messages based on **movement of the mouse pointer** instead of *actual changes in the slider position/value* (i.e., if you drag past the ends of the slider your "slider value changed" code keeps getting called even though the slider value is no longer changing). Maybe this is useful in some situations but I can't think of any. Even so, it should be a controllable behavior. **(This has now been recognized as an actual bug -- Bug ID# 2849637)** *It is controllable � bindings are value-oriented.  Bind the slider.*

*Neither of these is a real issue. Give NSSlider a custom NSSliderCell and draw whatever you want for both the knob and the bar. If you'd like an example I can point to at least one. And, getting your action as the mouse moves is controllable, store the last value and if the new value is the same just return.*

I'm guessing the action message based on mouse movement was introduced with the "knob" style of slider because that style has no "end points" and dragging the mouse further away from the control gives you finer control over the calculated value. For the good ol' fashioned slider bar, though, sending action messages when the slider vaue hasn't changed is a waste. Sure you can write code that ignores it, but why should you have to? The slider's function is make value changes, not indicate mouse movement.

----

***2004.09.30: NSSplitView (better control over collapse/expand behavior)** -- Most split views aren't usable below a certain width (or height). By adding "collapsePoint" attributes (and associated methods), it should be easy to set up splitviews through IB (i.e., no coding) that automatically collapse when the user "shrinks" them beyond a certain size and, converesely, automatically expand to a minimum size when the user starts to drag open (expand) a fully collapsed splitview.
----
**Vote: AYES 4, NAYS 0**

**Comments:**

*I've reported a similar issue to Apple a long time ago (about double-clicking the divider to collapse a subview.) It was recently updated behind Apple's Radar Iron Curtain, so perhaps it's getting some attention.*
--
I think the NSSplitView control should be revamped all together. It is a poor class. It has a lot of bugs, not much support for visual tweaking, it has a confusing superview/subview heirachy, it is damn hard to set the bounds of any of its subviews or slider position, and most of all NO AUTOSAVE!!!

----

***2004.10.04: Full Feature Set** --  Apple seems quite fond of introducing new UI elements in their software and they do it very well; it's easy to see that they have world class designers working for them. Unfortunately, they are rather slow at getting these new elements fully supported in Cocoa and IB. Just scanning a few days worth of discussions listed in RecentChanges will show you how often someone is trying to emulate something they've seen in Apple apps but find they have to subclass something (or add categories). Looking at some of the more recent apps (like Motion) and the new features in Tiger it's pretty obvious that there are a whole host of new UI elements on the way.

Making them natively supported (built into the AppKit) and available through IB would only improve the quality of 3rd-party apps. (*It would also give Apple more control, or at least influence, over how these elements get used*.) Not providing them makes it more difficult for 3rd-party developers to match the "look and feel" of popular Apple apps and, therefore, to an extent is like Apple competing with them. There's nothing wrong with such competition, per se, so long as it is actually beneficial. But as the sales rate of Mac computers has apparently (obviously?) crossed a threshold recently (meaning they could double their market share in the next year if they can produce that many machines), it would seem to be in their (and everyone's) best interest to provide 3rd-party developers with up-to-date support for as many UI elements as possible, as soon as possible. 
----
**Vote: AYES 3, NAYS 0**

**Comments:** Absolutely. It is hard for those of us with less experience to emulate the Apple UI if it isn't given to us in IB. Some of us just aren't experienced enough to make these subclasses or categories, and won't use a new UI element unless its in IB. NSSegmentedControl, for example. --GeoffPado 

Rejoice, then :) NSSegmentedControl, as well as many other new UI elements will be in IB in 10.4 (if you feel like searching through forums.macnn.com you can find a picture) --DavidSmith

*See http://forums.macnn.com/showthread.php?s=5bc84c2d024ca93758a56d0d60ec3881&threadid=233030&highlight=NSSegmentedControl#post2259510
*

----
***2005.02.22: Paragraph wells** We have the whole "well" concept in Cocoa; widgets that suck out a particular style from clippings drag-n-dropped onto them. We also have several semi-standardized style records, for text, color and fonts. So given that there are font and color wells, where's the paragraph well? Don't you want to be able to store your commonly used layouts in a cross-application panel? Don't you want to have a standard panel for this instead of just the ruler? Don't you want to handle all styles using the same UI? The UI seems broken in this respect, and has been all along.
----
**Vote: AYES 1, NAYS 2**

**Comments:**
I basically agree, although the whole idea of a paragraph well seems somewhat alien. The current state of play is absolutely borked as far as styles, both character and paragraph, are concerned. All the 'copy ruler' stuff is completely opaque to the user, and nobody ever invokes the character style panel, which is ugly as hell.


----



* **2005.01.23: Easier custom views in NSTableView** -- Right now, using an NSView subclass in an NSTableView is incredibly hard. There is very little sample code for doing it, and it requires a lot of work. Apple uses custom views in a lot of their apps (think the Downloads window in Safari), so why not make it easier to accomplish. 
----
**Vote: AYES 3, NAYS 0**

**Comments:**
Yes, this should be implemented. The cell class should have a setView: method. It should also be re-implemented into the NSMenuItem class as well - like it used to be.
*Yes.  Specifically, I wanted to put a progress indicator in a table cell, so I searched Apple's Cocoa mailing list archives.  There were questions about it going back to 2002, but no good answers.*
----

* **2006.03.21: NSSegmentedControl ought to have a vertical flavor** -- Right now, it is not possible to create a *column* of segmented controls, and one has to resort to a labor intensive and not as visually appealing set of NSButtons stacked on top of each other. 
----
**Vote: AYES 1, NAYS 2**

**Comments:**
Too many versions of the same thing serve only to confuse users. Is it really impossible to arrange your UI such that you can use the horizontal flavor? 

Rotate the view via setBoundsRotation?

***2006.08.20: A Rule Editor** : We need a Rule Editor with many options, available for Tiger through�an IB palette. Like this one:
http://perso.orange.fr/Ivanov/rule_editor.jpg

If anyone want to do such a thing (must not be very difficult), I am ready to contribute.
�----
**Vote: AYES 1, NAYS 0**
----

***2006.10.17: Boolean ops on Paths** -- It's hard to do some things in Quartz that were trivial in QuickDraw with Regions - things like forming the union, difference and intersection of closed paths. This really needs to be added to NSBezierPath. Consider the case of forming a path of a simple '+' sign. Not too hard, but even easier if you just overlay two rectangles at right angles and union them. This problem rapidly gets much harder for more complex shapes and paths. Note this is different from the "append path" feature - that's useful for knocking out holes in closed shapes, but not for taking out or adding pieces at the edges. For some operations you can fake it with clipping, but what about the case of stroking the above '+' sign's outline? Quartz currently has no way do that simple operation except forming the path one point at a time.
----
**Vote: AYES 4, NAYS 1**

**Comments:**
As much as I might like this, Quartz is non-device dependent and resolution independant.  Most libraries that support boolean operations on path including QuickDraw (AFAIK) do so by rasterizing and the tracing the rasterized image.  Rasterizing inherently reduces drawing fidelity to the resolution and device capabilities of the rasterization device.  Doing boolean operations on arbitrary vector paths is as they say a "non-trivial" operation.  Correct handling of antialiasing and transparenct makes it even harder.

Does anybody know how Adobe Illustrator implements boolean operations on paths ?  How about 3D Studio Max boolean operations on solids ?  Are there any Graphics Gems for this ? **YES: http://www.ics.uci.edu/~gopi/PAPERS/BOOLE.pdf **

*It's true that Regions are inherently a raster operation, which definitely makes this an easier problem to solve. However, I can't see what resolution has to do with it. A path is a list of (connected) points which define mathematical boundaries. It's perfectly possible, while working in that purely mathematical domain, to decide a) where two paths intersect, which forms new points on the path, b) which points of the additional path fall inside or outside of the first path, and which "side" (left or right) of the path they lie, and c) build a new list of points which add the intersection points and discard the unwanted points (those inside for a union, those outside for an intersection). The result is a path that can be rasterised (stroked and filled) as a new correctly formed path. Yes, it's hard to do in practice and the maths gets quite involved, and it's also hard to make it really fast, but that's not an argument for not doing it.*

**Some might argue that poor performance is a good reason not to do it.  Many people complained that Quartz was slow and said there is no reason to do anti-aliasing correctly if it is going to be so slow.**

*As an example of what you can't do in Quartz, think of Photoshop's selection tool - you can add and subtract arbitrary regions from the selecton simply by using the shift and option-shift keys as you drag.*
**Note:Photoshop operates on a bitmap of know resoluation and is not resolution idependant.  Quartz is resolution independnant**

*It's really easy to rapidly build up complex selection shapes. Just can't be done in Quartz out of the box. If Apple want to wean developers off QuickDraw and onto Quartz, then there needs to be parity of functionality across the two models.*
**Are you saying you would like Apple to add a fixed resolution bitmap oriented set of drawing operations to Quartz?  How would that be different from CoreImage ?**

*Quartz already does lots of things that can't be done in QD, but this remains one of the few where QD still has the advantage. Also, the code in NSBezierPath already has to compute all the intersection points so it can render paths that intersect correctly when there is an alpha value involved, so half the job is already being done privately.* 
**The calculation of intersection points for correct antialiased/transparent drawing happens at the rasterization stage and not in the "purely mathematical domain."  It is a vastly more difficult problem to calculate all of the intersection points without introducing a resolution dependance.  Quartz is by design resolution idenpendant until it is rasterized to a particular device.**

*There may be pathological cases that would mean that a totally general solution might not exist, but for the typical uses for this, which is building up more complex graphical paths from simpler ones, I can't see why it's not in there.*
**The simple answer may be that it is just a lot of work and possibly few developers would benefit.  Cost/benefit analysis...** 

*There are many papers and references on the web (though little code) and I will figure it out anyway, but my argument is that Apple should have this as a standard part of Quartz. --GrahamCox*
**Good luck.**

*Your reaction puzzles me. What it amounts to is: They shouldn't do it because it might suck. So it's better not to do it at all? Even a slow implementation is better than none at all. For me this would be an infrequent operation, so speed isn't absolutely vital. This has nothing to do with resolution dependence/independence. Regions are bitmap oriented operations (as is the whole of QD) but it doesn't mean a resolution-independent solution doesn't exist. If this were added tomorrow, I'd be very happy, but nobody would force you to use it, so what exactly is your problem with it? It would open up functionality that doesn't currently exist. How is that a bad thing? Also, I'm not remotely asking for bitmap-oriented operations in Quartz, I'm asking for exactly what I said - union, difference and intersection of paths as mathematically defined - you're the one dragging bitmaps into this because, for you, it's "too hard" to do it any other way. Your problem, not mine. Core Image has *nothing* to do with it. Seems to me that you're just one of those whiny people who just like to look at all the negatives (and post anonymously, to boot). Sure, it would be a lot of work, though obviously not for you, so what's your problem? If you can't see the benefits of having this, then you're not qualified to comment on it - far from only "a few developers would benefit", I believe it would open up a huge amount of power in the Quartz imaging model. Again, if you can't see that, that's your problem, not mine. (Also, learn to edit coherently - I've tidied up your formatting so it's actually readable). Anyone got anything sensible to contribute to this discussion?*
----
I thought we were voting.  Apparently anyone who votes NO is "not qualified to comment."
----
Vote anyway you like. However, I've yet to hear a good, coherent and technically sensible argument for NOT doing this. None of the above counts. I've looked extensively into this problem and am a long way towards solving it, so I do know what I'm talking about. So far the appeals to bitmaps and possible poor performance demonstrate that the poster of the comments doesn't have the slightest grasp of the problem, nor any real inkling of why it might be of very wide benefit. So let's bring some reasoned and informed comment into this, please. Incidentally, play with LineForm if you want a practical demonstration of the usefulness, practicality and performance of these operations. --GrahamCox
----

Things that are slow now will not necessarily be slow in five years. Personally, I'd love to be able to union, difference and calc the intersections of paths. Aye!

*I'm going to have to join the Ayes, mostly because I think I've found a solution. Namely, be lazy about making the union/intersection/xor, and store them as arrays of items to union/intersection/xor. Correct me if I'm wrong, but there's three groups of functions to do with graphics:
1) Render, as in Stroke or Fill - In these cases, we are rasterizing the graphic anyways, and we know, from the draw context, everything about the device. In these cases, render the subregions, perform the math with these rasters, and all is solved.
2) Mathematical, such as determine if a point is within a region, or to translate the region. Here, there is no device dependance, but at the same time, it can be broken down. IE, a point is in a union if it's in Subregion 1 OR Subregion 2. In an intersection if it's in Subregion 1 AND Subregion 2. Again, no heavy lifting.
3) Computation, such as creating a polygon from a bezier curve. These are CPU-intensive anyways for other graphics, and more importantly, I believe this is the rarest of the tasks (I have no proof either way). As such, it's okay if these are expensive, because they're so rare. --Blain*

---- 
I am certainly voting yes on this. There are algorithms to do this out there and they are well understood. It's called constructive area geometry (CAG) in 2d and constructive solid geometry (CSG) in 3d. Java has an implementation in its Area class, which may or may not be open source already, but if it isn't, there's always the ClassPath open source project that also has an implementation out for it. The algorithms aren't trivial but they aren't prohibitively expensive, and incorporating them would be extremely convenient for those of us who do need them. --DanielPeebles

