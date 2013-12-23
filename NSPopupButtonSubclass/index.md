---
layout: page
title: NSPopupButtonSubclass
---

I want an effect kinda like ButtonWithTwoStatesAndThreeAlternateImages but instead of toggling through them on click I want to choose from a menu.  What I am thinking of is an image that I can click on, have it popup a menu with choices about the display of the image.

What I thought I'd do is subclass NSPopupButton and override it's drawRect: method to draw the image based on the state of the submenu (which it gets with [self titleOfSelectedItem])  Is this a good way to do it? will the drawRect: method get called after the menu goes away? will I break something or is there an easy pitfall? (I have never subclassed any NSView before, but this time I need to do a couple and this seemed like the easiest one to start with)

----

Couldn't you just set the image for the NSPopupButton's menu items? Or use a NSImageView subclass with its     mouseDown:(NSEvent *)theEvent method overridden to show its     menu. Or use a NSPopupButton with the PullDown style. There are a few ways to do this, depending on exactly what you're trying to achieve - I don't think you need to subclass NSPopupButton.

----

Initial testing shows it works... I don't want image menu items, (because some of the states are related to text anyways),  I am not sure about showing and hiding menus... that seemed like more work than I wanted... I also don't want to show an arrow button.  Subclassing NSPopupButton and over-riding drawRect: was exactly the effect I wanted for how it works... now I just have to figure out the drawing bits...

----

further testing reveals the pitfalls of subclassing an NSControl.. I wanted to display images but I think that because NSPopupButton is an NSControl and uses an NSCell, they're all not antialiased. (ie. they are pixely!)  At anyrate, first responder... you were right... I just need to figure out the menu hiding and showing and then I should make it an NSView subclass (not NSImageView... that uses an NSCell too... wait-a-minute.... hmmmm)

----

Problem solved: see ImageAntialiasing

