---
layout: page
title: NSSliderBehaviour
---

I have customized the NSSlider & NSSliderCell and palettized it.

I can drag my slider onto the application window from the palette. But when I double click the slider in the nib, it is NSSliderCell which takes the user action instead of my customized cell.  
And it even displays the NSSliderCell instead of my customized cell. I have given some gradient effect for the slider bar in the cell. 

I have overridden  +cellClass() method to return the name of the cell's class in the initWithCoder method. 

Can anyone help me in this regard.

