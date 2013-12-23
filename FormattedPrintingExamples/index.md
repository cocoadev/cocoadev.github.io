---
layout: page
title: FormattedPrintingExamples
---



I am trying to improve my printed output for a small app of mine.    I am taking data which is displayed in an NSTableView, and trying to print out a similarly formatted view of the data.   I have basically replicated the print example from the Hillegass book, and I can print my data.   But, it's pretty ugly.     I've searched around, and found some good tips here, but code examples seem to be pretty sparse.

The type of examples I'm looking for are not complex graphical stuff, just text formatting things like:

 - Print a couple lines of summary at the top of the first page,  then print the table data with column headers.

 - Format data so it somewhat resembles the NSTableView.  e.g. the one large NSString field can vary in length.  I tried padding them out to the same size with spaces and using a monospace font (Courier), but I assume there is a more elegant method for doing this.

 - Maybe add a bit of formatting, like a horizontal line between the summary and the data, or lines between the columns / rows.

