---
layout: page
title: ToolbarSegmentedButton
---



I've added a Segmented Button to my toolbar, and it's looking a little off.  I'm trying to get something along the lines of the Finder window...

http://burgundylogan.com/pics/General/FunnyLookingSegmentedButton.jpg

The top is cut off and the images are not centered on the buttons.  Is there any way to fix this?  Thanks.

*Manually set the height.*

----

Try turning off unified title/toolbar look (which uses a slightly different height), and that should fix the clipping of the button. Your best bet would be to make the entire segmented button images yourself, instead of simply placing an image in the segment cell.

*I hate it when people take this approach, as it totally trashes the app's compatibility with themes applied by General/ShapeShifter and the like. I go the extra mile to keep my apps theme-saavvy.* - So your saying that if you were developing an app that could be made 5 times more easy to use by using a slick custom widget, that instead you would use stock theme-saavvy controls?

**And I hate it when people take the themes approach as it trashes some apps in general. This is a bad, bad thing to do to your system and has caused some odd issues on my own system when I tried it for the 'gee whiz' effect. My approach: I do not support problems caused by theme managers. Neither does Apple. For good reason.**

----

I fixed this problem by setting the toolbar item's height to that of the General/RoundTexturedButton, which is 23 pixels. As for the off-center images, just include some extra transparent space on the offending edges to offset the actual image. --General/JohnWells

----

Many General/NSControl<nowiki/>s actually require their height to be significantly greater than what's actually visible. For example, an General/NSButton's true view height is around twice what's reported as its height in IB. Presumably this is done to accomodate the shadow and things like that. If the view's height is set to what the height really is in IB, then things start to look weird and it tends to draw the title text off-center. This looks very similar.

----

In response to the issue with an off-center image, see http://developer.apple.com/documentation/Cocoa/Conceptual/General/SegmentedControl/index.html#//apple_ref/doc/uid/10000182i :

"In Mac OS X v10.4 and earlier, images are not automatically centered in the segment. To ensure that a segment's image is centered, use the following workaround:
[segmentedControl setLabel: nil forSegment: n];"

----

Not sure what the issue is, developers should not need to develop their applications with the themer in mind. If themers are serious about their theme-ing then they would create their own version of custom controls for which ever app doesn't respond to the new theme. Many of apple's applications themselves have custom controls built and don't respond to themes. People shouldn't be theme-ing OS X, and if you are don't copout and do it with shapeshifter. Thats the lazy way and it costs money. Do it the original way, the real way. Fire up that terminal!
