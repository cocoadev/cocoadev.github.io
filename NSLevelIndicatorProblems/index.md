---
layout: page
title: NSLevelIndicatorProblems
---

I think this is a bug in Tiger, but whenever I make an NSLevelIndicator, you can change its value by clicking in it.  Why does it do this?  I don't want it to!!! -RossDude

----

Why not subclass it and override     mouseDown:?

----

This seems to be a bug.  According to the 10.4 AppKit release notes,

*
If the cell is editable, the value can be changed by having the user track using the mouse or using the up/down or 
left/right arrows though only the rating one should be used in that manner. Any of the styles can be marked 
editable or have tick marks though in most cases, it only the ranking style should be editable and the continuous 
capacity indictor needs tick marks.
*

So,     levelIndicator cell] setEditable:NO] ought to disable the behavior.  However, this does not work.

----
If you are using bindings, make sure under the value popup, you uncheck "Conditionally Sets Enabled" as well as "Allows Editing Multiple Values Selection"
This goes for all [[NSControl / NSCell Subclasses
-- CJ

----
I have another NSLevelIndicator question.  I want my program to have a rating system (0 to 5 stars).  Before I used a dropdown box where they could select 0 to 5 but now I see they have the star-rating system with NSLevelIndicator.  I was wondering, however, if there was a way of having the stars a lighter gray when not active rather than invisible?  Right now if the rating is 0 stars it just shows up as nothing.  The only way of finding out there is a control there is by blindly clicking on the space.

*
Override the NSLevelIndicatorCell -isHighlighted method to always return YES. This will permanently show the dots.
-- Jeffrey
*

----
If I were you, I would write my own ratings indicator. The current implementation in NSLevelIndicator is less than stellar. If you notice, you have to click right on the dots to select a new value, making it harder to select. Plus, you cannot drag select just like every other indicator out there, including the one's Apple has used in iTunes and iPhoto.

----
To avoid values changes by clicking on the levelIndicator: [levelIndicator setEnabled:NO];
To allow values changes by dragging: [levelIndicator setContinuous:YES];
--Marc Pergand

----
If you want to disable this via Interface Builder without having to write code to handle it or dropping an outlet:
 
*Go to the bindings tab.
*Under "Availability" expand "enabled".
*"Bind to:" any available thing.
*Enter some "Model Key Path" that doesn't exist.
*Set your "Value Transformer" to "NSIsNotNil".
*Make sure "Raises for Not Applicable Keys" is NOT checked.

It will now always be disabled (as long as the keypath you entered doesn't exist, anyway.)

--Joshua Emmons

----
The NSLevelIndicatorCell class supports setting a custom image via setImage:
Maybe you can use that to display darker stars?

I've got problems of my own with this widget: When changing criticalValue and warningValue in a NSLevelIndicator of type NSContinuousCapacityLevelIndicatorStyle, the bar's colour will only be updated if you increase the values, not if you lower them. This looks like a bug. While I file a bug report and wait for Apple's answer, does anyone have an idea how to implement a progress bar with a customisable colour?

