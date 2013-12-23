---
layout: page
title: HowDoIDoRadioButtonsWithRegularButtons
---



Hi. After reading BookCocoaProgMacOSX and seeing a bit of the documentation, I am finally comfortable with working on Saratoga, an image editor that I want eventually to have a majority of Photoshop's features. I need to know this: I do not want to use radio buttons to allow the users to select a tool AND be notified of what tool is currently selected, nor do I want to use a segmented control because I need both rows and columns and eventually want to use images. So how do I make toggling regular buttons? - PietroGagliardi

----

Using an NSMatrix of NSButtonCell<nowiki/>s. Just set the NSButtonCell behavior to Toggle. You can do this in IB or programmatically using     setButtonType:.

----
Thanks. I was wondering what that Continuous meant. Now how do I remove the extra space between the buttons? I set the values in the Insepctor to 0.0, but nothing happens. List seems to be the only mode where a button is actually selected. Why? Finally, I deselect Allow selection by rect, but selection by rect still occurs. Is this a bug with the List mode?

