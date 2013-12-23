---
layout: page
title: CollapsingSplitViewsOnDoubleClick
---

<span style="color:red">**The approaches here are only valid on 10.4 and previous. See NSSplitView.**</span>

----

This code will collapse a split view if the user double clicks on the split bar.  Some issues:

* only written for verticle split http://goo.gl/OeSCu
* only collapses the left view
* when the user double clicks on the split bar we get multiple messages, the code to handle this is very hackish

-- SaileshAgrawal

This doesn't "collapse" a subview in the sense of -[NSSplitView isSubviewCollapsed:], it sets the width of the subview to 0.  There's a distinction.*

    /*************************************************************************************
     * Function - splitView:canCollapseSubview:
     *
     * Delegate function for SplitView, called when the user clicks on the split
     * bar.  I'm not sure what this function is supposed to do but I use it
     * to capture clicks on the splitBar.
     *
     * If the click was a double click then I collapse/uncollapse the leftView.
     * This requires some hacks.  When the view is uncollapsed and the user double
     * clicks it we get two messages.  When the view is collapsed and the user 
     * double clicks it we get one message.  Thus we keep a counter as follows:
     * x[-1 ==> 0] - the view is open so collapse it
     * x[ 0 ==> 1] - this is a redundant message so ignore it
     * x[ 1 ==> 2] - the view is close so uncollapse it and reset x to -1
     * Another hack is that I always return NO.  If I change this to a YES it
     * stops working.
    *************************************************************************************/
    - (BOOL)splitView:(NSSplitView *)sender canCollapseSubview:(NSView*)subview  {
        static int x = -1;     
        NSView *leftView = sender subviews] objectAtIndex:0];
        
        if (subview == leftView) {
            [[NSEvent *event = coverMatrix window] currentEvent];
            if ([event type] == [[NSLeftMouseDown && [event clickCount] == 2) {
                // keep count of the number of double click messages we get
                x++;
                
                // this is a redundant message so ignore it
                if (x == 1) return NO;
                
                if (coverMatrixCollapsed) {
                    [self uncollapseCoverMatrix:sender];
                    x = -1;
                } else {
                    [self collapseCoverMatrix:sender];
                }
            }
        }
        return NO;
    }
    
    /*************************************************************************************
     * Function - collapseCoverMatrix:
     *
     * Resize the left view so it's width is 0.  We save the old width before
     * collapsing it so if the user double clicks on the splitBar we can
     * restore the view to it's old width.
    *************************************************************************************/
    - (void) collapseCoverMatrix:(NSSplitView*)splitView {
        float oldPos;
        NSView *leftView=splitView subviews] objectAtIndex:0];
        [[NSView *rightView=splitView subviews] objectAtIndex:1]; 
        [[NSRect leftFrame=[leftView frame];
        NSRect rightFrame = [rightView frame]; 
        
        lastCoverMatrixWidth = leftFrame.size.width;
        lastWhiteViewWidth = rightFrame.size.width;
                
        rightFrame.size.width += leftFrame.size.width;
        [rightView setFrame:rightFrame];
        leftFrame.size.width = 0;
        [leftView setFrame:leftFrame];
        [splitView adjustSubviews];
        coverMatrixCollapsed = TRUE;
    }
    
    /*************************************************************************************
     * Function - uncollapseCoverMatrix:
     *
     * Restore the left view to it's previous size.
    *************************************************************************************/
    - (void) uncollapseCoverMatrix:(NSSplitView*)splitView {
        NSView *leftView=splitView subviews] objectAtIndex:0];
        [[NSView *rightView=splitView subviews] objectAtIndex:1]; 
        [[NSRect leftFrame=[leftView frame];
        NSRect rightFrame = [rightView frame]; 
    
        leftFrame.size.width = lastCoverMatrixWidth;
        [leftView setFrame:leftFrame];
    
        rightFrame.size.width = lastWhiteViewWidth;
        [rightView setFrame:rightFrame];
        
        [splitView adjustSubviews];
        coverMatrixCollapsed = FALSE;
    }

