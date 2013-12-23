---
layout: page
title: NSSegmentedControl
---

NSSegmentedControl is a subclass of NSControl that implements a horizontal control made of multiple segments. It provides a compact means of grouping together a number of controls.

Segments may contain text, images, and menus. One of the simplest applications of a segmented control is its use as a compact alternative to a group of radio buttons. Here, the user makes a single selection from a number of options. In a more complex example, segments can be independently selected or have associated menus. http://goo.gl/OeSCu

http://developer.apple.com/documentation/Cocoa/Conceptual/SegmentedControl/Art/example1.gif

Documentation: http://developer.apple.com/documentation/Cocoa/Conceptual/SegmentedControl/index.html

Just a note: a cell is not the same as a segment. The cell is the entire control, and segments are handled separately. And forget about using tags to distinguish between segments, the API does not make it easy.

----

The class doesn't show up in Interface Builder's class tab for me. I had to add it myself by subclassing NSControl. After that, I applied it as a custom class to an NSView, and set it up in code. Worked beautifully after that. I wonder why it's so hidden in InterfaceBuilder? It'd be much easier to set up if it was one of the standard controls that you can drag off from the palette.

----
Here is some sample code showing NSSegmentedControl.

http://www.nancesoftware.com/development/sample_code/segmentedcontrols/

JacobHazelgrove

I plan on writing a much more advanced sample, and I have been working on an IB Palette to access NSSegmentedControl.

----

The API is curiously incomplete.  Much can be done with NSSegmentedControl directly, but some can only happen with the cell.  You can set the title of a segment, but not the tag. You can't set the tracking mode on the control, only the cell.

----

You can set the tag for a segment:

    
segControl cell] setTag:0 forSegment:0];


then in the [[IBAction use:

    
int selectedSegment = [sender selectedSegment];
int clickedSegmentTag = sender cell] tagForSegment:selectedSegment];


[[JacobHazelgrove

----

Yeah sure, you can do that on the cell, but why are those particular calls vectored through the cell, and most of the others can be done through NSSegmentedControl?  It's an odd delineation.


----

Here is a IB Palette

http://www.droolingcat.com/software/nssegmentedcontrolpalette/


----

Just curious about the IB pallete: What is the best way to use an NSSegmentedControl?
Not like AHIG, but what would code for an action that the segmented control was connected to?
Keep in mind I'm talking about something from that palette switched to momentary.
Never mind, I figured it out. But I noticed that apple's code won't compile.
See:
http://developer.apple.com/documentation/Cocoa/Conceptual/SegmentedControl/index.html
The code     int clickedSegmentTag = sender cell] tagForSegment:selectedSegment]; should be     int clickedSegmentTag = [[sender cell] tagForSegment:[sender selectedSegment;
correct?

*No, because *    selectedSegment* was defined as an integer and assigned the address of the result of the *    [sender selectedSegment]* function. Your code will do the same, though.*

----

In Tiger's Interface Builder 2.5, NSSegmentedControl is available in the Cocoa Controls palette.


----

IB v437 tells me that NSSegmentedControl is compatible with OS10.2. However Xcode's v515's help tells me that it is only available in 10.3 and up. Which is true?

*Why don't you build for 10.2 only and find out? Install the extra SDKs from your XCode Tools installer.*

That won't help for things in IB. For that, you have to actually test on 10.2.

The segmented control requires 10.3.

*What IB is telling you is that it is going to use a nib format that's only compatible with 10.2 and up - namely it will use NSKeyedArchiver instead of NSArchiver.  That doesn't guarantee compatibility on 10.2, it just guarantees no compatibility on 10.1.*

----

This is utterly trivial, but I'm wondering if the window shown in this screenshot from apple's HIG is a custom type of toolbar or not, and how I could make one myself:

http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGLayout/chapter_19_section_5.html
(link is down. Guessing that http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGWindows/chapter_8_section_2.html contains the image you were talking about).

It's just that it looks cool... and as such I'm curious if it's a public API or if I have to use the new (10.3) segmented control class to "fake" the toolbar top. If it does require the segmented control class, can anybody show me how to set one of those up? The class documentation wasn't particularly helpful... perhaps there's a concept article?

...Later...
heh. nevermind.

http://developer.apple.com/documentation/Cocoa/Conceptual/SegmentedControl/index.html

I'm still wondering about that toolbar, though.

--ShamylZakariya

----

It looks like a NSSegmentedControl with square end caps... or maybe they're just sized out of the window's view. Or they could just be NSButtons with custom images and the toggle behavior. It does look nifty.

----

The toolbar is from Apple's Keynote. And it is just custom image buttons lined up at the top.

----

What a shame. I'll just use segmented buttons, then. I was hoping it'd just be a mystery constant passed to your NSToolBar. --ShamylZakariya

----
In iCal, the segmented control acts a little differently than NSSegmentedControl - when clicking it looks like momentary selection and when clicking the left or right arrow selection remains on the other selected cell. Here's a quick way to imitate this behavior:

Set your NSSegmentedControl to multiple selection. This avoids a flicker from changing selection and changing back when clicking the arrow buttons.

Next use -setMenu:forSegment: on each segment with an empty menu. This makes clicking the segment a dark blue like when it is set to momentary.

    
    NSMenu * fake = [[NSMenu alloc] init];
    [viewControl setMenu:fake forSegment:0];
    [viewControl setMenu:fake forSegment:1];
    [viewControl setMenu:fake forSegment:2];
    [viewControl setMenu:fake forSegment:3];
    [viewControl setMenu:fake forSegment:4];
    [fake release];


Next we set the selection to act like single selection in the target action:

    
- (IBAction)switchView:(id)sender
{
    static int  selectedSegment = -1;
    if ([sender isKindOfClass:[NSSegmentedControl class]])
    {
        switch ([viewControl selectedSegment])
        {
            case 0: // Left
                break;
            case 1: // Day
                selectedSegment = [viewControl selectedSegment];
                break;
            case 2: // Week
                selectedSegment = [viewControl selectedSegment];
                break;
            case 3: // Month
                selectedSegment = [viewControl selectedSegment];
                break;
            case 4: // Right
                break;
            default:
                break;
        }
        
        [viewControl setSelected:NO forSegment:0];
        [viewControl setSelected:(1 == selectedSegment) ? YES : NO forSegment:1];
        [viewControl setSelected:(2 == selectedSegment) ? YES : NO forSegment:2];
        [viewControl setSelected:(3 == selectedSegment) ? YES : NO forSegment:3];
        [viewControl setSelected:NO forSegment:4];
    }
}


I feel so dirty.

MatthewJimenez

----
The iCal style hack does not appear to work on Leopard, but iCal's segemented control for day/week/month appears to work normally in Leopard's iCal

Matthew Jimenez

----
This does work in Leopard, but I have 2 notes: 1) setting a menu for the Next/Previous segments is not necessary and 2) an additional if statement should be added as such (otherwise any segment before the selected one will be unselected):

    
- (IBAction)switchView:(id)sender
{
    static int  selectedSegment = -1;
    if ([sender isKindOfClass:[NSSegmentedControl class]])
    {
        switch ([viewControl selectedSegment])
        {
            case 0: // Left
                break;
            case 1: // Day
                selectedSegment = [viewControl selectedSegment];
                break;
            case 2: // Week
                selectedSegment = [viewControl selectedSegment];
                break;
            case 3: // Month
                selectedSegment = [viewControl selectedSegment];
                break;
            case 4: // Right
                break;
            default:
                break;
        }
        
        [viewControl setSelected:NO forSegment:0];
        [viewControl setSelected:NO forSegment:4];

        if(selectedSegment >= 0)
        {
                 [viewControl setSelected:(1 == selectedSegment) ? YES : NO forSegment:1];
                 [viewControl setSelected:(2 == selectedSegment) ? YES : NO forSegment:2];
                 [viewControl setSelected:(3 == selectedSegment) ? YES : NO forSegment:3];
        }
       
    }
}


Thanks for the original code; works well.

Ben Einstein

