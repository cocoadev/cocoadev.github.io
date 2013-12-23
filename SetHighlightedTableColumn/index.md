---
layout: page
title: SetHighlightedTableColumn
---



Am I not allowed to do this?  I try to do this, and setIndicatorImage, but neither of them work when the window opens.  Is there something I need to do, or does awakeFromNib not work for this, or what?

*Have you tried     selectColumnIndexes:byExtendingSelection:? Just call this on your table view. Also, I think whichever column is selected in Interface Builder will also be selected when you start the application. --JediKnil*

