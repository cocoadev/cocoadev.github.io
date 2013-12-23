---
layout: page
title: ShouldIBeUsingACustomView
---



I'm new to mac and Cocoa (though I do develop in other languages e.g Java) and could do with some insight into the best way of designing an Interface. I've been reading a lot of forums and web sites and I think I haven't got the hang of it yet. Let me explain.

I'm trying to write a crossword program. I'm trying to work out the best way of doing the cells/squares. If I was doing this in Java I would create a custom GUI class (probably a JPanel) that contains two labels one for the number (if needed) and one for the letter, and have an option to have it black or white as needed. I would then multiple instance of the class on another panel to form the crossword grid.

I thought that mabe the Custom View was the way to create custom GUI objects. Once created I would place them on the window where I wanted them (a bit like Visual Basic for those of you that know). I have not been able to find any examples, or people talking about this kind of thing, or any other reference, so I'm kind of thinking that maybe this isn't the way to go.

Can anyone please put me out of my misery and explain if my design concept if wrong and it would be best doing it another way. If the design concept is good, can anyone tell me about any articles on the web that I can read to get a better understanding of this.

I have my trusty Learning Cocoa with Objective-C but that doesn't cover this.

Thanks
----
For this, you probably should check out NSMatrix, and you'll probably populate it with a custom subclass of one of the existing subclasses of NSCell.
----
I respectfully disagree with the above poster.  Yes, a custom subclass of NSView is the basic mechanism for custom drawing.  Cocoa also has the concept of a 'cell', which is a helper object used by controls (NSControl is a subclass of NSView).  The good thing about cells is that they allow you to create your own objects that can be used in NSTableView or NSMatrix.  I.e. it makes your drawing more reusable.  The bad thing about cells is that the control-cell protocol is complicated.  

Since you aren't going to want to draw crossword entry items anywhere but in a crossword view, trying to understand the NSCell/NSMatrix interaction would likely be an unnecessary timesink.  You can either, (1) use a single large crossword view that contains lots of small entry views (you more or less suggested this), (2) forego the large object/small object division entirely and do all the drawing in the crossword view, or (3) write your own lightweight helper object that does the drawing.  Basically, your own NSCell that just does exactly what you want, and whose protocol you understand.

I would go with approach (2).  

 Approach (1) does not appeal to me because views are typically highly independent objects, and your crossword is not really composed of separable independently useful pieces.  Cocoa typically follows an MVC pattern, where data is not really contained in the view, it's pulled from the controller or model and displayed.  I would expect the crossword view to accept a datasource, and for the datasource to implement methods like -(NSString *)crosswordView:letterForRow:column: and -(NSRange)crosswordView:rangeOfAcrossWordAtRow:column:.  See NSTableDataSource for the model.  If you were to separate out each entry as a view, you wouldn't really be able to do that.  Each entry view would have to just have setLetter: and setDisplayedLocation: methods.  You'd have to drive the view hierarchy externally.  Another disadvantage of this approach is that it will influence your UI design, and make you favor designs that are chopped up into uniform cells.   Why shouldn't the crossword's corners be rounded, or why shouldn't the user's selection be presented as a ring that draws over top pieces of nearby cells?   Last, people have typically shied away from large numbers of NSViews for performance reasons.  

Approach (3) is a good one in general.  The Sketch project located at file:///Developer/Examples/AppKit/Sketch/ works this way.  For your case, I think it would just introduce unnecessary complexity; a method like -[CrosswordView drawEntryInRect:letter:location:] (and helper methods) on your CrosswordView should be fine.  I would start to think about breaking out a subobject if I started needing to store similar state in the view for each entry.  However, I don't think you need to store _any_ data in the crossword view at all, save for data regarding the user's selection.

