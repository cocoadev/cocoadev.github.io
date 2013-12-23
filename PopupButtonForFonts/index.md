---
layout: page
title: PopupButtonForFonts
---

I am creating a custom text editor and I don't want to use the default Font Panel which comes with NSTextView. I want to have a PopupButton that lists all the fonts and when the user selects a font the font is applied to NSTextView's selection, sort of like Font Popupmenu in Microsoft Word's Formatting Palette. But unfortunately I don't have a clue on how to implement it. - Saad R. Abbasi

----

Just populate the popup button with the available font names from NSFontManager and set the NSTextView's font in the button's action method

----

A question: why aren't you using the default Font Panel? You get a lot free from it, in terms of accessibility, font management, and forward compatibility. You can even add an accessory view if you need some custom controls. Users know how to use it already, too, so it softens your app's learning curve.

----

The user has always known how to use a font popup button too!! And Saad can still let the possibility to invoke the Font panel if the user wants to (for advanced options, for example)�

Flofl.

