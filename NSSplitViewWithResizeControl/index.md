---
layout: page
title: NSSplitViewWithResizeControl
---

NSSplitView has been improved with Leopard 10.5. To implement a resize control (like the one at the bottom of the MailStyleNSSplitview page), you'll need to set the delegate for the NSSplitView and then implement the following method in your delegate:

    -(NSView* )resizeView {
      // TODO: return the view which contains the resize control
    }
    
    -(NSView* )splitView {
      // TODO: return the split view
    }
    
    -(void)awakeFromNib {
      self splitView] setDelegate:self];
    }
    
    -([[NSRect)splitView:(NSSplitView *)splitView additionalEffectiveRectOfDividerAtIndex:(NSInteger)dividerIndex {
      return self resizeView] convertRect:[[self resizeView] bounds] toView:splitView]; 
    }
    


- [[DavidThorpe

