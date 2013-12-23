---
layout: page
title: ExactRGBColorCreation
---



I'm working on an application that needs to make blocks of RGB color and save them to a PDF file.  When I open the PDF file, in Adobe Illustrator for example, I need for the colors to have specific RGB values.

The problem is that all of the NSColor creation methods take values from 0 to 1.  How, then, can I ensure that a color I create in the application will have those specific 0-255 RGB values in the PDF?

----

*Divide the "255" values by 255 and you will have a value between 1.0 and 0.0, this is what you want.*

----

Yes, that's what I thought, but the numbers I'm getting in my pdf aren't always the same as the numbers I'm generating in the app.  I assumed that was due to some rounding.  I'll check my code again to make sure that I'm not truncating the numbers or something.

*Don't forget to convert one to a float :P e.g. divide by 255.0f*

(not OP:) The problem is that e.g. (0.1 == 0.1) isn't always true, since 0.1 doesn't have a binary floating point representation.  So exact color values can't be compared; you have to compare within a reasonable margin.  Beats me if there's a way to have NSColor do it, though - you might end up storing that state someplace else.  -- JoeOsborn

>>>> Why not just convert them to int and then compare?

----

How specific do those values have to be? I can guarantee that the colors in your PDF will be calibrated to your display at one time or another (if you are using Cocoa to do the rendering... I believe Acrobat and Illustrator also perform color correction). This could, for instance, cause a color of 0E3BFF to be renderd as 0C3AFF if your display device is slightly tinted reddish-green (yellow). Do you mean to be using a device color space? What other processing are you doing? How are you reading the values of those pixels when they are being displayed in Illustrator?

Just let me know if I'm barking up the wrong tree ;-)

-- EliotSimcoe

----

Indeed, the title of this page is meaningless, because there's no such thing as "exact" values of RGB colours. The numbers have to be defined within the context of some sort of colourspace, of which the 0-255 RGB is but one of an infinite number you could define. Yes, you can convert 0..255 ranges to 0..1 ranges with varying degrees of precision, but you still have to specify the colourspace. Quartz has a number of built-in 'standard' colour spaces, but the presence of ColorSync means that there are going to be subtle shifts in the colourspace as the colours move through the system. Question: Does using the device colourspace prevent this? --GrahamCox

----

Speaking historically, the device color space was uncalibrated in the NeXT days. I'm not sure whether this has changed in OS X.

----

I believe this is still the case, however NSColorSpace appears to apply calibration in the opposite way: "device" color space objects are calibrated, while "generic" color space objects are uncalibrated.

