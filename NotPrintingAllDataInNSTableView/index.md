---
layout: page
title: NotPrintingAllDataInNSTableView
---



I have written my first program (not a tutorial) using X-code - a simple amortization program.  The program logic works perfectly; I get an amortization schedule in my table view, and I get a vertical scroll bar to see the entire contents on screen. However, when I use the print menu item, all it will ever print is the "visible" view that is there when print is selected - I can't get any pagination so the entire table of data can be printed.  I created this as a simple application, not document application.  Does it have to be an NSDocument application to get the pagination for printing?  If so, is there any way to easily convert it?  Or would it be better/easier to call a print on the data array?  Thanks!

----

Too impatient you are, young Jedi. Faith you must have, that (within reason) print you can, anything from anywhere.

The short version is that if you want table-like printing, you draw the strings from your table cells in a custom view that you lay out and paginate yourself by deciding from your *printed* row heights how many rows-per-page to print. Your print view will be a subclass of NSView. You could initialize the view with the array of objects from your table data source and use those strings. Call the  job using     printShowingPrintPanel: from your window controller, and use NSPrintOperation. Or, yes, you could reformat the data in the data source as tabbed text and print that, e.g, from a text view. Opinions on the elegance of this may vary, but probably most will be pejorative. Though you are not using the document architecture, you might find the topics PrintingInADocumentBasedApp and PrintingDocumentBasedApplication helpful, especially their links to Apple's conceptual documentation on printing, and a reference to the example in Aaron Hillegass' classic Cocoa programming book. PrintingAView and PrintingArrayItemsWithVariableSize are discussions of some of the basics. All these topics can be located by searching for "printing" on this site. Now yours appears in that swelling list as well. To paraphrase the byline of the oft-maligned Wil Shipley, "Kittens are fuzzy. Research is fun."

----
I've just been along this path, writing a simple numerical analysis tool for my students. As the above poster notes, you need to handle all the layout on the printed page yourself. Loop over your data, work out the coordinates on the page where each value from your data array should be printed and use one of the NSString methods for drawing the string at that point. Again as the poster above notes, you'll need to subclass NSView and your subclass can call the same methods as your NSTableView does to retrieve the data points. Print Preview becomes your best friend while you are debugging. A good introduction is Chapter 24 in the Hillegass book (second ed.).

