---
layout: page
title: PrintingArrayItemsWithVariableSize
---

I am implementing my first printing system into my application. What I have is an array of strings (actually questions on an exam), variable in length from small to very large, that I want to print down the page, numbered of course, with answers underneath depending on whether it is a multiple choice question or not. Okay, I have implemented the printing by subclassing printShowingPrintPanel: and everything, implemented drawRect:, isFlipped, my init method and rectForPage. What I want to do is, if the question is too long to fit on the page after previous questions, have it skip down to the next page instead of cutting it off halfway or partway through the text. I have set up for a separate NSRect for each question and draw them as shown in Mac OS X Programming by AaronHillegass. The only problem is that his example doesn't account for variable item length, so I need a way to have the rects get their size by finding out how long the string will be in the final printing. I have no idea where to start and can't find anything near my situation on the net or here. If anyone has suggestions or ideas they are welcome. Thanks, --LoganCollins

----

You could try this NSString method, specified in AppKit Additions:

    
- (NSSize)sizeWithAttributes:(NSDictionary *)attributes

