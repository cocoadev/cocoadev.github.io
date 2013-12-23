---
layout: page
title: NSFontManagerHelp
---

Basically, i'm trying to make an app which will take an array of the available fonts on the computer, and put them all in an NSTableView.  Simple enough, right?

Here's what I wrote.

    

#import "Controller.h"

@implementation Controller

-(void)awakeFromNib
{
    fontManager = [NSFontManager sharedFontManager];
    array = [fontManager availableFonts];
}
- (IBAction)update:(id)sender
{
}
- (int)numberOfRowsInTableView:(NSTableView *)aTableView
{
    return [array count];
}
- (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
    return [array objectAtIndex:rowIndex];
}
@end



FontManager is an NSFontManager pointer... Array is an NSArray pointer.  Can someone help me out here?  Here's what happens when I compile and run.  I see a few fonts, but font all of them... then it crashes and says this 

## Component Manager: attempting to find symbols in a component alias of type (regR/carP/x!bt)

Font Tester has exited due to signal 10 (SIGBUS).

Thanks.

--CharlieMiller

----

availableFonts is autoreleased... remember to retain it !

/PtxMac

----

Ah great, now I feel like an idiot.  Thanks :)

----

Cool. Now tell me what 

## Component Manager: attempting to find symbols in a component alias of type (regR/carP/x!bt) 

means. I get it when I build on my iBook but not when I build on my other platform, a G4 tower

----

Do you have Suitcase or anything else that messes with fonts installed on the iBook? (and not on the G4)

----

It's a bug in Roxio Toast 5 VCD plug-in. Either delete /Library/QuickTime/Toast Video CD Support.qtx or get Toast 6.

