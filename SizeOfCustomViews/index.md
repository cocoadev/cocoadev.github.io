---
layout: page
title: SizeOfCustomViews
---

I am a bit confused about how resolution independent I can make my own custom views.

Imagine I need to display a label, I can use NSFont's labelFontSize to get the font size, but am I in fact not making a mistake by using this selector, as my graphics will be drawn for the size that my own system reports, and the size of my entire custom view is set in InterfaceBuilder, and does not change, even if the label font has twice the size on the end users system!?!

----

Huh ... I suppose that's a good point. Don't statically define UI elements in which the user can change the font; instead get the metrics for the selected font, resize your UI item to fit, then draw the text. NSTextFieldCell is handy for this kind of thing; you can associate text with font information, get the size, and then draw the cell in your view. Jaguar introduced some new string drawing API (categories on NSString IIRC) that also serve this purpose.

In this specific case of drawing an Aqua label, I suggest explicitly setting the label font and size as described in the Mac OS X Aqua Interface Guidelines and just statically laying out the item in InterfaceBuilder. Lucida Grande cannot be disabled or removed, and a reasonable person can assume it is available on all Mac OS X systems. And since that's basically what NSFont's labelFont* class methods are doing for you already, maybe that's good enough for the forseeable future. But perhaps this is a hypothetical situation ("Imagine I need to...") -- your mileage may vary depending on your driving habits and road conditions.

-- MikeTrent

