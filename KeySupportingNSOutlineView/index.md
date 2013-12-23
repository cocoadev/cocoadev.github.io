---
layout: page
title: KeySupportingNSOutlineView
---



Below is an NSOutlineView subclass which overloads keyDown: so that


* arrow left/right will collapse/expand current item,
* return will send *action* to *target*,
* space will brings up the field editor for the first column which can be edited.


Question: Is there anyway to dismiss the field editor via the keyboard? � I think the ESC key will work. *But it doesn't.*

And: Is there any reason Apple hasn't made similar functionality built-in?

----
    
@implementation KeySupportingOutlineView

- (void)keyDown:(NSEvent*)theEvent
{
   BOOL swallow = NO;
   if([self numberOfSelectedRows] == 1)
   {
      id item = [self itemAtRow:[self selectedRow]];
      NSString* keyString = [theEvent characters];
      switch([keyString length] == 1 ? [keyString characterAtIndex:0] : 0)
      {
         case NSLeftArrowFunctionKey:
         {
            if(self dataSource] outlineView:self isItemExpandable:item] == YES)
               [self collapseItem:item], swallow = YES;
         }
         break;

         case [[NSRightArrowFunctionKey:
         {
            if(self dataSource] outlineView:self isItemExpandable:item] == YES)
               [self expandItem:item], swallow = YES;
         }
         break;

         case '\r':
         {
            if([self action])
               swallow = [[[NSApp sendAction:[self action] to:[self target] from:self];
         }
         break;

         case ' ':
         {
            NSArray* columns = [self tableColumns];
            for(int i = 0; i < [columns count]; i++)
            {
               NSTableColumn* column = [columns objectAtIndex:i];
               if([column isEditable] == YES)
               {
                  id delegate = [self delegate];
                  if(delegate && [delegate respondsToSelector:@selector(outlineView:shouldEditTableColumn:item:)] && [delegate outlineView:self shouldEditTableColumn:column item:item] == NO)
                     continue;
   
                  [self editColumn:i row:[self selectedRow] withEvent:nil select:YES];
                  swallow = YES;
                  break;
               }
            }
         }
         break;
      }
   }

   if(swallow == NO)
      [super keyDown:theEvent];
}

@end

