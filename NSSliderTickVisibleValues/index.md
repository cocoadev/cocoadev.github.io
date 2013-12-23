---
layout: page
title: NSSliderTickVisibleValues
---

Question: How do I use NSSlider and make it so tick values are Visible?


Visually I want something like

    
 |-----|-----|-----|-----|
 0     1     2     3     4


Any good sugestions on how to get the 0,1,2,3,4 numbers to appear....

----
Just add labels around your slider in the appropriate place in IB.

I fixed up your formatting so that it made sense. See TextFormattingRules and HowToUseThisSite.
----
In the Attributes inspector of IB, set the number of markers to soemthing other than zero.  That will make the tick marks visible.  You will have to place the labels on the tick marks yourself.  Just drag non-editable strings from a palette and position them to taste.

