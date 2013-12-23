---
layout: page
title: ProblemWithLoadNibNamed
---

I'm having a problem creating a custom view. I make a new empty nib and throw some stuff into it.  Then I make a subclass of NSView to be the File owner:

    
#ifndef __FREQVIEW_H__
#define __FREQVIEW_H__
/* FreqController */
#include "TestView.h"
#import <Cocoa/Cocoa.h>

@interface FreqView : TestView
{
    IBOutlet id nSteps;
    IBOutlet id startFreq;
    IBOutlet id startFreqUnit;
    IBOutlet id stepPercent;
    IBOutlet id stepSize;
    IBOutlet id stepSizeUnit;
    IBOutlet id stopFreq;
    IBOutlet id stopFreqUnit;
}

- (void)gatherInfo;

@end

#endif /* __FREQVIEW_H__ */


TestView inherits NSView.  Now, I have an initialization method like so:

    
- (id)initWithCore:(coremod_t*)cmod
{
   if(![NSBundle loadNibNamed:@"FreqViewPackage.nib" owner: self ])
      NSLog(@"can't get nib");

   [ super initWithCore: cmod ];
   return self;
}


When I load the nib, I get this error:

2005-06-25 20:36:57.780 hrdwaretst[523] *** Uncaught exception: <NSInternalInconsistencyException> Error (1002) creating CGSWindow

----

Your init method needs to call your superclass's init method. NSView's designated initializer is initWithFrame:. So you need to change your init method to:

    
- (id)initWithCore:(coremod_t*)cmod
{
   self = [super initWithFrame:NSZeroRect];
   if(![NSBundle loadNibNamed:@"FreqViewPackage.nib" owner: self ])
      NSLog(@"can't get nib");

   [ super initWithCore: cmod ];
   return self;
}


No, that's completely wrong. Whatever class is directly descended from NSView must call initWithFrame, but FreqView is not directly descended from NSView. Its superclass is TestView, and apparently TestView's designated initializer is initWithCore:. It is TestView's responsibility to call initWithFrame:.

You should not be calling init twice here. This is even more wrong, and has the great potential to break things.

----

I thought the code in question was for TestView, which is a subclass of NSView. And I didn't see the call to [super initWithCore:]. But the basic idea, call the superclass's initializer first (before you try to load a nib or something), is an important one. So:

    
- (id)initWithCore:(coremod_t*)cmod
{
   self = [super initWithCore: cmod];
   if(self) {
      if(![NSBundle loadNibNamed:@"FreqViewPackage.nib" owner: self ])
         NSLog(@"can't get nib");
   }
   return self;
}


----

Yes, very good point, and one which I completely missed. Trying to load the nib before the view is initialized is probably a very bad idea. Also, don't forget to make sure that super's init statement returned a sensible value (if statement inserted into code above), although that's not likely to be causing the problem at hand.

----

Thanks so much to everybody who replied.  While it wasn't the cause of the problem, the initWithCore method is a great suggestion.

It turns out that all of this code was running before NSApplicationMain.  When I initialize all of the modules from the main controller's awakeFromNib method, all is well.

