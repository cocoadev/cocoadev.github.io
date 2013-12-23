---
layout: page
title: EditorWithBreakpoints
---



Hi,

I am wanting to create an editor that is capable of displaying breakpoints.  It seems that NSRulerView and NSRulerMarker would work splendidly for this except for the fact that I can't hide the measurement tick marks (unless I am missing something).  Should I create a new subclass of NSView to handle this or am I making this more complicated than it needs to be?

Many Thanks!
LauRence

----

try this:

    

// MyTextView.h

#import <AppKit/AppKit.h>

@interface MyTextView : NSScrollView {
}
@end

@interface MyRulerView : NSRulerView {
}
@end

// MyTextView.m

#import "UmTextView.h"

@implementation MyTextView

- (id)initWithFrame:(NSRect)frame {
    id textView, rulerView;
    self = [super initWithFrame:frame];
    if (self) {
        rulerView = [[[MyRulerView alloc ] init ] autorelease ];
        textView = [[[NSTextView alloc ] initWithFrame: frame ] autorelease ];
        [self setDocumentView:textView ];
        [self setRulersVisible:YES ];
        [self setHasHorizontalRuler:YES ];
        [self setBorderType:NSGrooveBorder ];
        [self setHorizontalRulerView:rulerView ];
        [self setDrawsBackground:YES ];
    }
    return self;
}
@end

@implementation MyRulerView 
-(void)drawHashMarksAndLabelsInRect:(NSRect)rect {
// the only purpose here is to override this method and do nothing
}
@end




Hope you don't get confused when I call an NSScrollView MyTextView (this is what IB does, so I got into the habit of doing it). Interface Builder's Text View is actually a scroll view with an NSTextView as the document view. 

Not sure how far along you are with your program, so here's a couple of ways you can use this custom object.

1. If you already have a scroll view with a ruler view, just replace the ruler view with MyRulerView. 

2. If you want to use MyTextView as a custom view in IB do the following:
 *drag the object file (MyTextView.h) from PB to the MainMenu.nib in IB
 *drag and drop a Custom View object into a window.
 *select the Custom View by clicking on it
 *hit Command-1 to bring the info window to the front
 *select Custom Class from the drop down menu in the info window.
 *select MyTextView
    
now make the needed connections and add the IBOutlet to your controller object

-- zootbobbalu

