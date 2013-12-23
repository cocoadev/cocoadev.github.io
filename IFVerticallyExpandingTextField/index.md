---
layout: page
title: IFVerticallyExpandingTextField
---

   This textfield expands and contracts as it is edited and resized, and behaves
   in a similar manner to the input field in Apple's iChat message windows.

   Superviews of the textfield and the window containing the textfield autosize
   accordingly.

-- AndrewBowman

IFVerticallyExpandingTextField.h
----
    
/*
 Copyright (c) 2006, Andrew Bowman.  All rights reserved.
 
 Redistribution and use in source and binary forms, with or without 
 modification, are permitted provided that the following conditions are met:
 
 * Redistributions of source code must retain the above copyright notice, this 
   list of conditions and the following disclaimer.
 * Redistributions in binary form must reproduce the above copyright notice, 
   this list of conditions and the following disclaimer in the documentation 
   and/or other materials provided with the distribution.
 * Neither the name of Inverse Falcon nor the names of its contributors may be 
   used to endorse or promote products derived from this software without 
   specific prior written permission.
 
 THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND 
 ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED 
 WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE 
 DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
 ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES 
 (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; 
  LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON 
 ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT 
 (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS 
 SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 */

/* IFVerticallyExpandingTextField 

   This textfield expands and contracts as it is edited and resized, and behaves
   in a similar manner to the input field in Apple's iChat message windows.

   Superviews of the textfield and the window containing the textfield autosize 
   accordingly.

*/


#import <Cocoa/Cocoa.h>

enum { IFVerticalPadding = 5 };


@interface IFVerticallyExpandingTextField : NSTextField
{
   BOOL superviewsExpandOnGrowth;
   BOOL isCollapsed;
   NSMutableArray *viewMaskPairs; 
}

- (void) awakeFromNib;
- (void) setSuperviewsExpandOnGrowth: (BOOL)shouldExpand;
- (BOOL) superviewsExpandOnGrowth;
- (void) forceAutosize;

@end




IFVerticallyExpandingTextField.m
----
    

/*
 Copyright (c) 2006, Andrew Bowman.  All rights reserved.
 
 Redistribution and use in source and binary forms, with or without 
 modification, are permitted provided that the following conditions are met:
 
 * Redistributions of source code must retain the above copyright notice, this 
 list of conditions and the following disclaimer.
 * Redistributions in binary form must reproduce the above copyright notice, 
 this list of conditions and the following disclaimer in the documentation 
 and/or other materials provided with the distribution.
 * Neither the name of Inverse Falcon nor the names of its contributors may be 
 used to endorse or promote products derived from this software without 
 specific prior written permission.
 
 THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND 
 ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED 
 WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE 
 DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
 ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES 
 (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; 
  LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON 
 ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT 
 (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS 
 SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 */

#import "IFVerticallyExpandingTextField.h"

// Private helper class for associating NSViews with autoresizingMasks
@interface IFViewMaskPair : NSObject 
{   
   NSView *view;
   unsigned int savedAutoresizingMask;
}

- (id) initWithView: (NSView *)aView;
- (void) restoreAutoresizingMask;

@end


@implementation IFViewMaskPair 

- (id) initWithView: (NSView *)aView {
   self = [super init];
   view = aView;
   savedAutoresizingMask = [view autoresizingMask];
   
   return self;
}

- (void) restoreAutoresizingMask {
   [view setAutoresizingMask: savedAutoresizingMask];
}

@end



@interface IFVerticallyExpandingTextField (PRIVATE)

- (void) autosizeHeight: (NSTextView *)fieldEditor;
- (void) autosizeSuperviewOfView: (NSView *)originView withGrowth: (float)growth;

- (void) alterAutoresizeMasksForViews: (NSArray *)sibViews 
                      surroundingView: (NSView *)originView;
- (void) restoreAutoresizeMasks;

@end


@implementation IFVerticallyExpandingTextField

- (void) awakeFromNib {
   superviewsExpandOnGrowth = YES;
   isCollapsed = NO;
   viewMaskPairs = [[NSMutableArray alloc] init];
   
   if ([self autoresizingMask] & NSViewHeightSizable) {
      [self setAutoresizingMask: [self autoresizingMask] & ~NSViewHeightSizable];
      
      NSLog(@"Warning: IFVerticallyExpandingTextField: Vertical autosizing option "
            "in Interface Builder interferes with this class's functionality and has "
            "been temporarily disabled.  Turn off this option for all "
            "IFVerticallyExpandingTextFields in Interface Builder to prevent this warning.");
   }
}

- (void) setSuperviewsExpandOnGrowth: (BOOL)shouldExpand {
   superviewsExpandOnGrowth = shouldExpand;
}

- (BOOL) superviewsExpandOnGrowth {
   return superviewsExpandOnGrowth;
}

- (void) forceAutosize {
   
   // Entry point for vertical expansion.  Call this method if you need to manually
   // force an autosize.  Most of the time this is done for you in response to the 
   // textDidChange and viewDidEndLiveResize callbacks.
   //
   // Note that if we're forced to steal the field editor and first responder status,
   // quirky behavior can occur if we just throw first responder back to whoever 
   // had it last (especially with several expanding text fields), so we resign 
   // first responder.
   
   BOOL stolenEditor = NO;
   NSWindow *myWindow = [self window];
   NSTextView *fieldEditor = [myWindow fieldEditor: YES forObject: self];
   
   if ([fieldEditor delegate] != self) {
      stolenEditor = YES;
      
      [myWindow endEditingFor: nil];
      [myWindow makeFirstResponder: self];
      
      // Set cursor to end, breaking the selection
      [fieldEditor setSelectedRange: NSMakeRange(self stringValue] length], 0)];
   }
   
   [self autosizeHeight: fieldEditor];
   
   if (stolenEditor) {   
      // Odd things can occur when messing with the first responder when using 
      // several [[IFVerticallyExpandingTextFields.  Best not to mess with it, for now.
      
      [myWindow makeFirstResponder: nil];
   }
}


/* Private methods */

- (void) autosizeHeight: (NSTextView *)fieldEditor {
   NSRect newFrame = [self frame];
   float oldHeight = newFrame.size.height;
   float newHeight;
   float fieldGrowth;
   
   if (isCollapsed)
      newHeight = 0;
   else
      newHeight = fieldEditor layoutManager] usedRectForTextContainer:
         [fieldEditor textContainer.size.height + IFVerticalPadding;
   
   fieldGrowth = newHeight - oldHeight;   
   
   if (fieldGrowth != 0) {
      
      // We're expanding or contracting. First adjust our frame, 
      // then see about superviews.
      
      newFrame.size = NSMakeSize(newFrame.size.width, newHeight);
      
      if ([self autoresizingMask] & NSViewMinYMargin)
         newFrame.origin.y -= fieldGrowth;
      
      [self setFrame: newFrame];
      
      if (superviewsExpandOnGrowth) {
         [self autosizeSuperviewOfView: self withGrowth: fieldGrowth];
      }

      // If superviews are set not to expand on growth, it's best to call display
      // on the window in reponse to this notification to prevent artifacts.
      [[NSNotificationCenter defaultCenter] postNotificationName: @"IFTextFieldDidExpandNotification"
                                                          object: self
                                                        userInfo: 
         [NSDictionary dictionaryWithObject: [NSNumber numberWithFloat: fieldGrowth]
                                     forKey: @"IFTextFieldNotificationFieldGrowthItem"]];
   }
}



- (void) autosizeSuperviewOfView: (NSView *)originView withGrowth: (float)growth {
   
   // Recursively autosize superviews until we get to a window or scroll view
   
   NSView *currentView = [originView superview];  // current view we are working in
   
   [self alterAutoresizeMasksForViews: [currentView subviews] surroundingView: originView];
   
   if (currentView == originView window] contentView]) {
      // First base case, stop recursion when we've reached window's content view
      
      [[NSWindow *myWindow = [originView window];
      NSRect windowFrame = [myWindow frame];
      
      windowFrame.size.height += growth;
      windowFrame.origin.y -= growth;
      
      // Using animate: YES causes some brief drawing artifacts and makes multiple
      // troublesome viewDidEndLiveResize calls.  Stick with NO for now.
      [myWindow setFrame: windowFrame display: [myWindow isVisible] animate: NO];
      
      [self restoreAutoresizeMasks];
   }
   else if ([currentView isKindOfClass: [NSScrollView class]]) {
      // Second base case, stop at scrollviews.
      // Trying to get scrollviews' content to expand.
      // Scrollview blocks do appear, but with no arrows or scrolling controls
      // Some help here would be appreciated
      
      NSScrollView *scrollView = (NSScrollView *) currentView;
      NSRect contentFrame = scrollView contentView] frame];
      
      contentFrame.size.height += growth;
      contentFrame.origin.y -= growth;
      
      [[scrollView contentView] setFrame: contentFrame];
      [scrollView tile];
      
      [self restoreAutoresizeMasks];
      
   }
   else {
      // Recursive case, modify our current frame then step up to its superview
      
      [[NSRect currentFrame = [currentView frame];
      currentFrame.size.height += growth;
      currentFrame.origin.y -= growth;
      
      [currentView setFrame: currentFrame];
      
      [self autosizeSuperviewOfView: currentView withGrowth: growth];
   }
   
}


- (void) alterAutoresizeMasksForViews: (NSArray *)siblingViews 
                         surroundingView: (NSView *)originView {
   
   // We need to alter the autoresizing masks of surrounding views so they don't 
   // mess up the originView's vertical expansion or contraction.
   //
   // This method uses BSD-licensed code from the Disclosable View application 
   // copyright (c) 2002, Kurt Revis of Snoize (www.snoize.com) 
   
   NSEnumerator *enumerator = [siblingViews objectEnumerator];
   NSView *sibView;
   unsigned int mask;
   
   while (sibView = [enumerator nextObject]) {
      if (sibView != originView) {
         
         // save autoresizingMask for restoration later
         [viewMaskPairs addObject: 
            [[[IFViewMaskPair alloc] initWithView: sibView] autorelease]];
         
         mask = [sibView autoresizingMask];
         
         if (NSMaxY([sibView frame]) <= NSMaxY([originView frame])) {
            // This subview is below us. Make it stick to the bottom of the window.
            // It should not change height.
            mask &= ~NSViewHeightSizable;
            mask |= NSViewMaxYMargin;
            mask &= ~NSViewMinYMargin;
         } 
         else {
            // This subview is above us. Make it stick to the top of the window.
            // It should not change height.
            mask &= ~NSViewHeightSizable;
            mask &= ~NSViewMaxYMargin;
            mask |= NSViewMinYMargin;
         }
         
         [sibView setAutoresizingMask: mask];
      }
   }
}

- (void) restoreAutoresizeMasks {
   IFViewMaskPair *pair;
   
   while ([viewMaskPairs count]) {
      pair = [viewMaskPairs lastObject];
      [pair restoreAutoresizingMask];
      [viewMaskPairs removeLastObject];
   }
}


/* Overridden methods */

- (void) textDidChange: (NSNotification *)note {
   [self forceAutosize];
}

- (void) viewDidEndLiveResize {
   [self forceAutosize];
}

- (void) setStringValue: (NSString *)aString {
   NSTextView *myEditor = [self currentEditor];
   
   if (myEditor)
      [myEditor setString: aString];
   else
      [super setStringValue: aString];
   
   // If we don't delay, autosizing won't display correctly
   [NSThread sleepUntilDate: [NSDate dateWithTimeIntervalSinceNow: .05]];
   [self forceAutosize];
}

- (void) setCollapsed: (BOOL)flag {
   if (isCollapsed != flag) {
      isCollapsed = flag;
      [self setHidden: flag];
      [self forceAutosize];
   }
}

- (BOOL) isCollapsed {
   return isCollapsed;
}

- (void) dealloc {
   [viewMaskPairs release];
   [super dealloc];
}

@end


----
Give it a try!  You should be able to throw this into a project, read the files in Interface Builder, and use the custom class pane to make an NSTextField into an IFVerticallyExpandingTextField.  You'll need to set the layout and linebreaking attributes for word wrapping for this to work.  

Although expansion should work properly when the textfield is embedded in a subview, I'm having some trouble dealing with NSScrollView<nowiki/>s.  The field expands into the scrollview's content view, but none of the controls on the scrollbar appear.  Some help here would be appreciated.
----
Added the setStringValue method, can't believe I forgot that.  In my test application I thought I saw a noticeable difference in speed when calling setString on the field editor rather than setStringValue on super, but maybe it was just my imagination.  Can anyone confirm this?

Also added a slight time delay in this method to prevent display problems when autosizing.
----
Added the setHidden method.  I haven't tested this one out, so I don't know what the consequences are if the field is set visible when a view has been moved up to where the field should be.
----
You're not using setHidden: correctly. setHidden: visually hides the field, but -- by design -- does not hide it for layout purposes. You want to add a new method, setCollapsed: or setMinimized:. You shouldn't need to override setHidden.
----
Thanks for the correction.  I've changed the code to use setCollapsed.  Have you had the chance to play around with this?  Any feedback's welcome.
-- AndrewBowman
----
I know this post is a bit old, but it was a good starting point for me.  Here's a suggestion for improving the code: use a separate NSTextView as a utility object to determine the required height rather than the field editor.  This avoids the issue with moving first responder around unnecessarily.  You'd also probably want to cache the text view rather than create it each time.  Here's a sample of what I'm talking about:
    
    NSTextView *utilityTextView = [[NSTextView alloc] initWithFrame:[self frame]];
    [utilityTextView setString:[self stringValue]];
    utilityTextView layoutManager] glyphRangeForTextContainer:[utilityTextView textContainer; // force layout
    CGFloat newHeight = NSHeight(utilityTextView layoutManager] usedRectForTextContainer:[utilityTextView textContainer);

-- Sean Rich

