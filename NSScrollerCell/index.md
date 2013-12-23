---
layout: page
title: NSScrollerCell
---

Yeah, I know that it doesn't exist, but my question is WHY?

NSSlider -> NSSliderCell and NSStepper -> NSStepperCell etc.

-- JP

----

I'd assume that it's generally not considered useful outside the context of another view. Steppers are generally intended for use with text views, no? Therefore, it would make less sense to have e.g. a matrix full of steppers than a matrix full of sliders (like an E.Q. for an audio program or something to do with histagrams). What would you want to use NSStepperCell for? -- RobRix

Did anyone check to see if it actually does lack? or if instead, it is (as with so many other classes) just undocumented?

----

I was looking for a NSScrollerCell(doesn't exist), not NSStepperCell(does exist) :)
In my current project(a SVG editor) some elements may have horizontal and vertical scrollers. The whole point is to have MacOS X style scrollers, and not to make my own. -- JP

----

Sorry, misunderstanding on my part. I'm afraid I don't know what to suggest... anything I can think of would be horribly hackish and evil. -- RobRix

----

So is that a BUG or a FEATURE??? (inconsistency) -- JP

----

I don't think it should be ridiculously hard to make.  Unfortunately, I'm too dumb to know how to make it.
--CharlieMiller

----

dont look for scrollercell.... look for NSScrollView.  put yourView into one of these, it completely takes care of scrollers, and gives you hooks for making the kinds of changes you are talking about. 
in interface builder, select your view, and select the menuItem: Layout:make subviews of...:scroll view.  make a connection to it and you can programatically tell it to have particular scrollers, and what increments to scroll.

----

(I am going to use 'svg' element as an example, and I copied some text from http://www.w3.org/TR/REC-CSS2/visufx.html )

Problem 1: 'svg' elements may be nested (This is one of the reasons why NSScroller control is a no go, but a NSScrollerCell would be perfect)

Problem 2: The 'overflow' property(This property specifies clipping type):

'visible' - This value indicates that content is not clipped,(That means that if i would use a NSScrollView or a NSView, then I must resize it to fit the current viewport ? the document window or another 'svg' element)

'hidden' - This value indicates that the content is clipped and that no scrolling mechanism should be provided to view the content outside the clipping region; users will not have access to clipped content. The size and shape of the clipping region is specified by the 'clip'(Problem 3) property. (NSView would be perfect here,  but see problem number 3!)

'scroll' - This value indicates that the content is clipped and that if the user agent uses scrolling mechanism that is visible on the screen (such as a scroll bar or a panner), that mechanism should be displayed for a box whether or not any of its content is clipped. (NSScrollView would be perfect here,  but see problem number 3!)

'auto' - The behavior of the 'auto' value is user agent-dependent, but should cause a scrolling mechanism to be provided for overflowing boxes. (In other words possible values are 'hidden' or 'scroll')

Problem 3: the 'clip' property(This property specifies clipping rect's offset):

'rect(<top offset>, <right offset> ,<bottom offset> , <left offset>)' - This eliminates the potential use of NSScrollView(doh, because each control has its own initial clipping rect called 'frame').

-- JP (BTW, English isn't my native language)

----

This brings to mind:

* 1 - NSView<nowiki/>s may be nested.
* 2 - As for 'visible', you can probably make use of NSView's -superview method for this.
* 3 - I don't really understand why the clipping rect offset should keep you from using NSScrollView; why can't you set the frame of the view you're using to display the content, whether it's NSScrollView or some mythical JPGenericSVGElementView?


Oh, and don't worry too much about your English, it's more than adequate. -- RobRix

----

More potential problems:


*4. Drawing knob handles over the NSScrollerView.
*5. Mouse actions. (If the view is not selected, first left click on it should select it and after that it should be possible to scroll it.)


-- JP

----

JP, there is a "NSScroller.h"

which is a subclass of NSControl, I suggest you try working with one and see what happens.  I found it in the Appkit Framework header files.  sounds just like what you might be looking for.
eblu

