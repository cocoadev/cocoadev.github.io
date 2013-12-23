---
layout: page
title: DisableRowSelectionBasedOnMouseClick
---

I wish to have one column of my outline view dedicated to switch buttons, and when this column is clicked, the item should NOT be selected (as in iTunes), unfortunately I have not found a way to do it.

I tried this hacky solution, but my outlineView:shouldSelectItem: selector is called twice for each mouse down, causing the item to be toggled twice. Any help?
    
- (BOOL)outlineView:(NSOutlineView *)outlineView shouldSelectItem:(id)item
{
   NSEvent *theEvent = [NSApp currentEvent];
   if([theEvent type] == NSLeftMouseDown)
   {
      NSPoint p = [outlineView convertPoint:[theEvent locationInWindow] fromView:nil];
      NSRect r = [outlineView rectOfColumn:0];
      if(NSPointInRect(p, r))
      {
         [item setEnabled:([item enabled] ? NO : YES)];
         [outlineView reloadItem:item];
         return NO;
      }
   }
   return YES;
}

