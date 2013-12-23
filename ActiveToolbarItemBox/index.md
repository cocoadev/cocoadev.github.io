---
layout: page
title: ActiveToolbarItemBox
---

In Panther (ex: System Preferences), the active toolbar item has a box around it. How is this achieved? --GarrettMurray

From the release notes: *The toolbar delegate must implement -toolbarSelectableItemIdentifiers to indicate which type of toolbar items can be used to indicate mode.*

And of course, this is extremely useful as well: � setSelectedItemIdentifier:

