---
layout: page
title: TableCellWithTextInDifferentColorsInSameCell
---



Is it possible to have text in table cells with different colors? I was hoping that General/NSTableView understood some kind of markup that would allow me to define e.g. colors in the text string.

----

General/NSCell has a     setAttributedStringValue: method that takes an General/NSAttributedString object.
