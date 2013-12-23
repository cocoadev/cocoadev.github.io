---
layout: page
title: CollectionViewForTiger
---

This is a NSCollectionView like class that works on Tiger. It derives directly from NSView. I tried to customize NSTableView but there were too many bugs with drag/drop and scrollbars for it too work. If you don't need those features then you might want to start there first (see  http://www.joar.com/code ). --SaileshAgrawal

This is written specifically with the requirements I had in mind. It has the following features and limitations hard coded into it:

* Like NSCollectionView allows you to create you row's view in Interface Builder and add it to the collection view.
* One column only
* Always has at least one selected item.
* Allows multiple selection.
* Allows reordering of items using drag/drop.
* Allows files to be dragged into it.
* Allows rows to be dragged out of it as shortcuts to files.
* Automatically changes text field colors to white when a row is selected.
* Your row view must derive from TigerCollectionViewItem to be added to the collection view.
* Allows menus on right click


Remember that this is mostly sample code. Feel free to use it any way you like but there will likely be some bugs in it.

----
**Interface**

    
#import <Cocoa/Cocoa.h>


@class TigerCollectionViewItem;


@protocol TigerCollectionViewTarget <NSObject>

- (NSDragOperation)dragOperationForFiles: (NSArray *)filePaths;
- (void)dragFiles: (NSArray *)filePaths toIndex: (int)index;
- (void)dragItemsAtIndexes: (NSArray *)indexes toIndex: (int)index;
- (BOOL)shouldRemoveItemsAtIndexes: (NSArray *)indexes;
- (void)performDoubleClickActionForIndex: (int)index;
- (void)onSelectionDidChange;
- (NSString *)filePathForIndex: (int)index;

@end


@interface TigerCollectionView : NSView
{
   IBOutlet id<TigerCollectionViewTarget> target;
   NSMutableArray *items;
   BOOL needsLayout;
}

- (void)addItem: (TigerCollectionViewItem *)item
        atIndex: (int)index;

- (void)removeItemAtIndex: (int)index;

- (void)removeAllItems;

- (int)numberOfItems;

- (NSArray *)selectedIndexes;

@end


@interface TigerCollectionViewItem : NSView
{
   BOOL isSelected;
   NSPoint mouseDownPos;
   int dragTargetType;
   NSMutableDictionary *cachedTextColors;
}
@end



----
**Implementation**

    
#import "TigerCollectionView.h"


static const float DRAG_START_DISTANCE = 10;
static const float DRAG_IMAGE_ALPHA = 0.5;
static NSString * const DRAG_ITEM_TYPE = @"TigerCollectionViewDragType";

typedef enum
{
   DragTargetType_None,
   DragTargetType_Top,
   DragTargetType_Bottom,
} DragTargetType;


@interface TigerCollectionView (Private)

// Override NSView
- (void)moveDown: (id)sender;
- (void)moveUp: (id)sender;
- (BOOL)isFlipped;
- (void)selectAll: (id)sender;
- (void)keyDown: (NSEvent *)event;
- (void)deleteBackward: (id)sender;
- (void)deleteForward: (id)sender;
- (NSDragOperation)draggingEntered: (id<NSDraggingInfo>)sender;
- (void)draggingExited: (id<NSDraggingInfo>)sender;
- (BOOL)prepareForDragOperation: (id<NSDraggingInfo>)sender;
- (BOOL)performDragOperation: (id<NSDraggingInfo>)sender;
- (NSDragOperation)draggingUpdated: (id<NSDraggingInfo>)sender;
- (BOOL)wantsPeriodicDraggingUpdates;
- (NSDragOperation)draggingSourceOperationMaskForLocal: (BOOL)isLocal;
- (void)resizeWithOldSuperviewSize: (NSSize)oldBoundsSize;

// Internal methods
- (void)setNeedsLayout: (BOOL)flag;
- (void)performLayout;
- (void)startDrag: (NSEvent *)event
         withItem: (TigerCollectionViewItem *)item;
- (NSImage *)dragImageForIndexes: (NSArray *)indexes
                     dragPoint: (NSPoint *)dragPoint;
- (void)itemClicked: (TigerCollectionViewItem *)item
              event: (NSEvent *)event;
- (void)setAllItemsSelected: (BOOL)selected;
- (void)growSelectionToItem: (TigerCollectionViewItem *)item;
- (void)moveSelection: (BOOL)moveUp
          byExtending: (BOOL)byExtending;
- (void)scrollToItem: (TigerCollectionViewItem *)item;
- (void)maintainNonEmptySelection: (int)index;
- (void)removeItemsAtIndexes: (NSArray *)indexes;
- (int)indexFromDragTarget: (NSView *)targetView
              draggingInfo: (id<NSDraggingInfo>)draggingInfo;
- (void)setDragTarget: (NSView *)targetView
         draggingInfo: (id<NSDraggingInfo>)draggingInfo;
- (void)setIndex: (int)index
    isDragTarget: (BOOL)isDragTarget;
- (id<TigerCollectionViewTarget>)target;
- (int)dragTargetIndex;
- (void)maximizeViewWidth: (id)sender;
- (void)onKeyWindowChanged: (NSNotification *)notification;
- (void)testSelectionChanged: (NSArray *)oldSelection;
- (NSArray *)filePathsForIndexes: (NSArray *)indexes;

@end


@interface TigerCollectionViewItem (Private)

// Override NSView
- (NSMenu *)menuForEvent:(NSEvent *)event;
- (NSView *)hitTest: (NSPoint)point;
- (void)mouseDown: (NSEvent *)event;
- (void)mouseUp: (NSEvent *)event;
- (void)mouseDragged: (NSEvent *)event;
- (BOOL)acceptsFirstResponder;

// Internal methods
- (void)setIsSelected: (BOOL)flag;
- (BOOL)isSelected;
- (TigerCollectionView *)collectionView;
- (BOOL)isLeftClickEvent: (NSEvent *)event;
- (void)setDragTargetType: (DragTargetType)type;
- (DragTargetType)dragTargetType;
- (void)updateHighlightState;
- (void)setHighlight: (BOOL)isHighlighted
        forTextField: (NSTextField *)textField;
- (BOOL)shouldDrawSecondaryHighlight;

@end


static float
PointDistance(NSPoint start,
              NSPoint end)
{
   float deltaX = end.x - start.x;
   float deltaY = end.y - start.y;
   return sqrt(deltaX * deltaX + deltaY * deltaY);
}


@implementation TigerCollectionView


- (id)initWithFrame: (NSRect)frame
{
   if ((self = [super initWithFrame:frame])) {
      items = [[NSMutableArray alloc] init];
   }
   return self;
}


- (void)dealloc
{
   [[NSNotificationCenter defaultCenter] removeObserver:self];
   [items release];
   items = nil;
   [super dealloc];
}


- (void)awakeFromNib
{
   ASSERT(self target] conformsToProtocol:
      @protocol([[TigerCollectionViewTarget)]);

   [[NSNotificationCenter defaultCenter]
      addObserver:self
         selector:@selector(onKeyWindowChanged:)
             name:NSWindowDidBecomeKeyNotification
           object:[self window]];
   [[NSNotificationCenter defaultCenter]
      addObserver:self
         selector:@selector(onKeyWindowChanged:)
             name:NSWindowDidResignKeyNotification
           object:[self window]];

   [self enclosingScrollView] contentView] setCopiesOnScroll:NO];

   [self registerForDraggedTypes:
      [[[NSArray arrayWithObjects:NSFilenamesPboardType, DRAG_ITEM_TYPE, nil]];

   [self maximizeViewWidth:nil];
}


- (void)drawRect: (NSRect)rect
{
   /*
    * XXX: This is a hack to work around autohide scrollbars not working
    * correctly. When removing an item causes the vertical scrollbar to
    * be hidden our items don't automatically resize to fit the new width.
    *
    * Until that bug is fixed this hack disables the "needsLayout" optimization
    * and forces a layout on every dray. The layout code is pretty fast so this
    * isn't very expensive.
    */
   needsLayout = YES;

   if (needsLayout) {
      [self performLayout];
      [self maintainNonEmptySelection:0];
   }
   [super drawRect:rect];

   [[NSColor colorWithCalibratedRed:214.0/255.0
                              green:221.0/255.0
                               blue:229.0/255.0
                              alpha:1.0] set];
   NSRectFill([self bounds]);
}


- (void)addItem: (TigerCollectionViewItem *)item
        atIndex: (int)index
{
   ASSERT(item);
   [items insertObject:item atIndex:index];
   [item setAutoresizingMask:NSViewWidthSizable | NSViewMinYMargin];
   [self addSubview:item];
   [self setNeedsLayout:YES];
}


- (void)removeItemAtIndex: (int)index
{
   [self removeItemsAtIndexes:
      [NSArray arrayWithObject:[NSNumber numberWithInt:index]]];
   [self maintainNonEmptySelection:index];
}

- (void)removeAllItems
{
   [items removeAllObjects];
   [self setNeedsLayout:YES];
   [self setNeedsDisplay:YES];
}


- (int)numberOfItems
{
   return [items count];
}


- (NSArray *)selectedIndexes
{
   NSMutableArray *selectedIndexes = [NSMutableArray array];
   int count = [items count];
   int i;
   for (i = 0; i < count; i++) {
      TigerCollectionViewItem *item = [items objectAtIndex:i];
      if ([item isSelected]) {
         [selectedIndexes addObject:[NSNumber numberWithInt:i]];
      }
   }
   return selectedIndexes;
}


@end // TigerCollectionView


@implementation TigerCollectionView (Private)


- (void)setNeedsLayout: (BOOL)flag
{
   needsLayout = flag;
}


- (void)performLayout
{
   // Calculate the total height.
   float myHeight = 0;
   NSEnumerator *e = [items objectEnumerator];
   TigerCollectionViewItem *item;
   while ((item = [e nextObject])) {
      myHeight += [item frame].size.height;
   }

   // Resize the collection view to fit.
   NSRect myFrame = [self frame];
   [self setFrameSize:NSMakeSize(myFrame.size.width, myHeight)];
   if (myFrame.size.height != myHeight) {
      [self setNeedsDisplay:YES];
   }

   // Layout all the items.
   float yPos = 0;
   e = [items objectEnumerator];
   while ((item = [e nextObject])) {
      NSRect oldItemFrame = [item frame];
      NSRect newItemFrame;
      newItemFrame.origin.y = yPos;
      newItemFrame.origin.x = 0;
      newItemFrame.size.width = myFrame.size.width;
      newItemFrame.size.height = oldItemFrame.size.height;
      [item setFrame:newItemFrame];

      yPos += newItemFrame.size.height;
      if (!NSEqualRects(newItemFrame, oldItemFrame)) {
         [item setNeedsDisplay:YES];
      }
   }

   [self setNeedsLayout:NO];
}


- (BOOL)isFlipped
{
   return YES;
}


- (void)startDrag: (NSEvent *)event
         withItem: (TigerCollectionViewItem *)item
{
   // If the dragged item is selected then drag all selected items too.
   NSArray *dragIndexes = nil;
   if ([item isSelected]) {
      dragIndexes = [self selectedIndexes];
   } else {
      dragIndexes = [NSArray arrayWithObject:
         [NSNumber numberWithInt:[items indexOfObject:item]]];
   }

   // Write data to the paste board.
   NSPasteboard *pboard = [NSPasteboard pasteboardWithName:NSDragPboard];
   [pboard declareTypes:[NSArray arrayWithObjects:DRAG_ITEM_TYPE,
                                                  NSFilenamesPboardType,
                                                  nil]
                  owner:self];
   [pboard setPropertyList:dragIndexes
                   forType:DRAG_ITEM_TYPE];
   [pboard setPropertyList:[self filePathsForIndexes:dragIndexes]
                   forType:NSFilenamesPboardType];

   // Generate the drag image from the dragged items.
   NSPoint dragPos;
   NSImage *dragImage = [self dragImageForIndexes:dragIndexes dragPoint:&dragPos];

   // Start the drag.
   [self dragImage:dragImage
                at:dragPos
            offset:NSZeroSize
             event:event
        pasteboard:pboard
            source:self
         slideBack:YES];
}


- (NSImage *)dragImageForIndexes: (NSArray *)indexes
                     dragPoint: (NSPoint *)dragPoint
{
   // Make an image as big as the visible rect.
   NSRect dragRect = [self convertRect:[self visibleRect]
                              fromView:[self superview]];
   NSImage *dragImage =
      [[[NSImage alloc] initWithSize:dragRect.size] autorelease];

   NSEnumerator *e = [indexes objectEnumerator];
   NSNumber *indexNumber;
   while ((indexNumber = [e nextObject])) {
      TigerCollectionViewItem *item =
         [items objectAtIndex:[indexNumber intValue]];

      NSRect itemRect = [item visibleRect];
      if (NSEqualRects(itemRect, NSZeroRect)) {
         continue;
      }

      // Get an image of the dragged view without the selection.
      BOOL oldSelectedValue = [item isSelected];
      [item setIsSelected:NO];
      NSData *itemAsPDF = [item dataWithPDFInsideRect:itemRect];
      [item setIsSelected:oldSelectedValue];
      NSImage *itemImage = [[NSImage alloc] initWithData:itemAsPDF];

      // Convert from our flipped axis into the image's non-flipped axis.
      NSPoint pos = [item frame].origin;
      pos.y = dragRect.origin.y + dragRect.size.height - pos.y;
      pos.y -= itemRect.size.height;

      // Drag the view's image into the drag image.
      [dragImage lockFocus];
      [itemImage drawAtPoint:pos
                    fromRect:NSZeroRect
                   operation:NSCompositeSourceOver
                    fraction:DRAG_IMAGE_ALPHA];
      [dragImage unlockFocus];

      [itemImage release];
   }

   ASSERT(dragPoint);
   *dragPoint = NSMakePoint(dragRect.origin.x,
                            dragRect.origin.y + dragRect.size.height);

   return dragImage;
}


- (void)itemClicked: (TigerCollectionViewItem *)item
              event: (NSEvent *)event
{
   NSArray *oldSelection = [self selectedIndexes];

   if (([event modifierFlags] & NSCommandKeyMask) != 0) {
      [item setIsSelected:![item isSelected]];
   } else if (([event modifierFlags] & NSShiftKeyMask) != 0) {
      [self growSelectionToItem:item];
   } else {
      [self setAllItemsSelected:NO];
      [item setIsSelected:YES];
      if ([event clickCount] == 2) {
         self target]
            performDoubleClickActionForIndex:[items indexOfObject:item;
      }
   }
   [self scrollToItem:item];

   [self testSelectionChanged:oldSelection];
}


- (void)setAllItemsSelected: (BOOL)selected
{
   NSEnumerator *e = [items objectEnumerator];
   TigerCollectionViewItem *item;
   while ((item = [e nextObject])) {
      [item setIsSelected:selected];
   }
}


- (void)growSelectionToItem: (TigerCollectionViewItem *)item
{
   NSArray *oldSelection = [self selectedIndexes];

   int itemIndex = [items indexOfObject:item];
   int startIndex = oldSelection objectAtIndex:0] intValue];
   int endIndex = [[oldSelection lastObject] intValue];

   if (itemIndex < startIndex) {
      startIndex = itemIndex;
   } else if (itemIndex > endIndex) {
      endIndex = itemIndex;
   }

   int i;
   for (i = startIndex; i <= endIndex; i++) {
      [[items objectAtIndex:i] setIsSelected:YES];
   }

   [self testSelectionChanged:oldSelection];
}


- (void)moveDown: (id)sender
{
   BOOL shift = ([[[[NSApp currentEvent] modifierFlags] & NSShiftKeyMask) != 0;
   [self moveSelection:NO byExtending:shift];
}


- (void)moveUp: (id)sender
{
   BOOL shift = ([[NSApp currentEvent] modifierFlags] & NSShiftKeyMask) != 0;
   [self moveSelection:YES byExtending:shift];
}


- (void)moveSelection: (BOOL)moveUp
          byExtending: (BOOL)byExtending
{
   if ([items count] == 0) {
      return;
   }
   NSArray *oldSelection = [self selectedIndexes];

   int index = NSNotFound;
   if (moveUp) {
      int firstIndex = oldSelection objectAtIndex:0] intValue];
      firstIndex--;
      if (firstIndex >= 0) {
         index = firstIndex;
      }
   } else {
      int lastIndex = [[oldSelection lastObject] intValue];
      lastIndex++;
      if (lastIndex < [items count]) {
         index = lastIndex;
      }
   }

   if (index != [[NSNotFound) {
      if (!byExtending) {
         [self setAllItemsSelected:NO];
      }
      TigerCollectionViewItem *item = [items objectAtIndex:index];
      [item setIsSelected:YES];
      [self scrollToItem:item];
   }

   [self testSelectionChanged:oldSelection];
}


- (void)scrollToItem: (TigerCollectionViewItem *)item
{
   NSRect itemFrame = [item frame];
   NSPoint top, bottom;
   bottom.y = NSMaxY(itemFrame);
   top.y = NSMinY(itemFrame);
   top.x = 0;
   bottom.x = 0;

   NSRect visibleRect = [self visibleRect];
   BOOL bottomVisible = NSPointInRect(bottom, visibleRect);
   BOOL topVisible = NSPointInRect(top, visibleRect);
   if (!topVisible || !bottomVisible) {
      NSPoint scrollPos;
      if (!bottomVisible) {
         scrollPos.y = bottom.y - visibleRect.size.height;
      } else {
         scrollPos.y = top.y;
      }
      scrollPos.x = 0;

      NSClipView *clipView = self enclosingScrollView] contentView];
      [clipView scrollToPoint:[clipView constrainScrollPoint:scrollPos;
      self enclosingScrollView] reflectScrolledClipView:clipView];
   }
}


- (void)selectAll: (id)sender
{
   [[NSArray *oldSelection = [self selectedIndexes];
   [self setAllItemsSelected:YES];
   [self testSelectionChanged:oldSelection];
}


- (void)maintainNonEmptySelection: (int)index
{
   NSArray *oldSelection = [self selectedIndexes];
   if ([items count] > 0 && [oldSelection count] == 0) {
      int selectionIndex = index;
      if (selectionIndex < 0) {
         selectionIndex = 0;
      } else if (selectionIndex >= [items count]) {
         selectionIndex = [items count] - 1;
      }
      items objectAtIndex:selectionIndex] setIsSelected:YES];

      [self testSelectionChanged:oldSelection];
   }
}


- (void)keyDown: ([[NSEvent *)event
{
   unichar u = event charactersIgnoringModifiers] characterAtIndex: 0];

   if (u == [[NSDeleteCharacter ||
       u == NSDeleteFunctionKey) {
      // Forward or backward delete.
      [self interpretKeyEvents:[NSArray arrayWithObject:event]];
   } else if (u == NSEnterCharacter ||
              u == NSCarriageReturnCharacter) {
      NSArray *indexes = [self selectedIndexes];
      if ([indexes count] > 0) {
         self target] performDoubleClickActionForIndex:
            [[indexes objectAtIndex:0] intValue;
      }
   } else {
      [super keyDown:event];
   }
}


- (void)deleteBackward: (id)sender
{
   NSArray *indexes = [self selectedIndexes];
   if ([indexes count] > 0 &&
       self target] shouldRemoveItemsAtIndexes:indexes]) {
      [self removeItemsAtIndexes:indexes];
      [self maintainNonEmptySelection:[[indexes objectAtIndex:0] intValue] - 1];
      [[self window] makeFirstResponder:self];
   }
}


- (void)deleteForward: (id)sender
{
   [[NSArray *indexes = [self selectedIndexes];
   if ([indexes count] > 0 &&
       self target] shouldRemoveItemsAtIndexes:indexes]) {
      [self removeItemsAtIndexes:indexes];
      [self maintainNonEmptySelection:[[indexes objectAtIndex:0] intValue;
      self window] makeFirstResponder:self];
   }
}


- (void)removeItemsAtIndexes: ([[NSArray *)indexes
{
   if ([indexes count] == 0) {
      return;
   }

   NSMutableIndexSet *indexSet = [NSMutableIndexSet indexSet];

   NSEnumerator *e = [indexes objectEnumerator];
   NSNumber *indexNumber;
   while ((indexNumber = [e nextObject])) {
      int index = [indexNumber intValue];
      TigerCollectionViewItem *item = [items objectAtIndex:index];
      [item removeFromSuperview];
      [indexSet addIndex:index];
   }

   [items removeObjectsAtIndexes:indexSet];

   /*
    * Need to force the layout to change right away so that scrolling and
    * selection code will work.
    */
   [self performLayout];
}


- (NSDragOperation)draggingEntered: (id<NSDraggingInfo>)sender
{
   return [self draggingUpdated:sender];
}


- (void)draggingExited: (id<NSDraggingInfo>)sender
{
   [self setDragTarget:nil draggingInfo:sender];
}


- (BOOL)prepareForDragOperation: (id<NSDraggingInfo>)sender
{
   BOOL acceptDrop = NO;
   NSArray *dragTypes = sender draggingPasteboard] types];

   if ([dragTypes containsObject:DRAG_ITEM_TYPE]) {
      acceptDrop = YES;
   } else if ([dragTypes containsObject:[[NSFilenamesPboardType]) {
      NSArray *filePaths = sender draggingPasteboard]
         propertyListForType:[[NSFilenamesPboardType];
      acceptDrop = self target] dragOperationForFiles:filePaths] !=
                   [[NSDragOperationNone;
   }

   if (!acceptDrop) {
      [self setDragTarget:nil draggingInfo:sender];
   }
   return acceptDrop;
}


- (BOOL)performDragOperation: (id<NSDraggingInfo>)sender
{
   int destIndex = [self dragTargetIndex];
   ASSERT(destIndex != NSNotFound);
   [self setDragTarget:nil draggingInfo:sender];

   NSArray *dragTypes = sender draggingPasteboard] types];
   if ([dragTypes containsObject:DRAG_ITEM_TYPE]) {
      [[NSArray *indexes = sender draggingPasteboard]
         propertyListForType:DRAG_ITEM_TYPE];
      [[self target] dragItemsAtIndexes:indexes toIndex:destIndex];         
   } else if ([dragTypes containsObject:[[NSFilenamesPboardType]) {
      NSArray *filePaths = sender draggingPasteboard]
         propertyListForType:[[NSFilenamesPboardType];
      self target] dragFiles:filePaths toIndex:destIndex];
   }
   return YES;
}


- ([[NSDragOperation)draggingUpdated: (id<NSDraggingInfo>)sender
{
   NSPoint dragPos = self superview] convertPoint:[sender draggingLocation]
                                           fromView:nil];
   [[NSView *targetView = [self hitTest:dragPos];

   NSDragOperation dragOperation = NSDragOperationNone;
   NSArray *dragTypes = sender draggingPasteboard] types];
   if ([dragTypes containsObject:DRAG_ITEM_TYPE]) {
      dragOperation = [[NSDragOperationMove;
   } else if ([dragTypes containsObject:NSFilenamesPboardType]) {
      NSArray *filePaths = sender draggingPasteboard]
         propertyListForType:[[NSFilenamesPboardType];
      dragOperation = self target] dragOperationForFiles:filePaths];
   }

   if (dragOperation == [[NSDragOperationNone) {
      [self setDragTarget:nil draggingInfo:sender];
   } else {
      [self setDragTarget:targetView draggingInfo:sender];
   }
   return dragOperation;
}


- (BOOL)wantsPeriodicDraggingUpdates
{
   return NO;
}


- (NSDragOperation)draggingSourceOperationMaskForLocal: (BOOL)isLocal
{
   if (isLocal) {
      return NSDragOperationMove;
   } else {
      return NSDragOperationLink;
   }
}


- (int)indexFromDragTarget: (NSView *)targetView
              draggingInfo: (id<NSDraggingInfo>)draggingInfo
{
   int index = NSNotFound;
   if (targetView &&
       [targetView isKindOfClass:[TigerCollectionViewItem class]]) {
      index = [items indexOfObject:targetView];
   }

   if (index != NSNotFound) {
      TigerCollectionViewItem *item =
         [items objectAtIndex:index];
      NSPoint viewPos = [item convertPoint:[draggingInfo draggingLocation]
                                  fromView:nil];
      NSRect bounds = [item bounds];
      if (viewPos.y < bounds.size.height / 2.0) {
         index = fmin(index + 1, [items count]);
      }
   }

   return index;
}


- (void)setDragTarget: (NSView *)targetView
         draggingInfo: (id<NSDraggingInfo>)draggingInfo
{
   int newDragTargetIndex = [self indexFromDragTarget:targetView
                                         draggingInfo:draggingInfo];
   int curDragTargetIndex = [self dragTargetIndex];
   if (newDragTargetIndex != curDragTargetIndex) {
      [self setIndex:curDragTargetIndex isDragTarget:NO];
      [self setIndex:newDragTargetIndex isDragTarget:YES];
   }
}


- (void)setIndex: (int)index
    isDragTarget: (BOOL)isDragTarget
{
   if (index != NSNotFound) {
      DragTargetType dragTargetType = isDragTarget ? DragTargetType_Top :
                                                     DragTargetType_None;
      int actualIndex = index;
      if (actualIndex == [items count]) {
         actualIndex = [items count] - 1;
         if (isDragTarget) {
            dragTargetType = DragTargetType_Bottom;
         }
      }
      items objectAtIndex:actualIndex] setDragTargetType:dragTargetType];
   }
}


- (id<[[TigerCollectionViewTarget>)target
{
   return target;
}


- (int)dragTargetIndex
{
   int count = [items count];
   int i;
   for (i = 0; i < count; i++) {
      TigerCollectionViewItem *item = [items objectAtIndex:i];
      if ([item dragTargetType] == DragTargetType_Top) {
         return i;
      } else if ([item dragTargetType] == DragTargetType_Bottom) {
         return i + 1;
      }
   }
   return NSNotFound;
}


- (void)resizeWithOldSuperviewSize: (NSSize)oldBoundsSize
{
   [super resizeWithOldSuperviewSize:oldBoundsSize];
   [self performSelector:@selector(maximizeViewWidth:) withObject:nil afterDelay:0.10];
   //[self maximizeViewWidth:nil];
}


- (void)maximizeViewWidth: (id)sender
{
   float width = [self enclosingScrollView] contentView] frame].size.width;
   [[NSRect myOldFrame = [self frame];
   if (myOldFrame.size.width != width) {
      [self setFrameSize:NSMakeSize(width, myOldFrame.size.height)];
      [self setNeedsDisplay:YES];
   }
}


- (void)onKeyWindowChanged: (NSNotification *)notification
{
   [self setNeedsDisplay:YES];

   NSEnumerator *e = [items objectEnumerator];
   TigerCollectionViewItem *item;
   while ((item = [e nextObject])) {
      [item updateHighlightState];
   }
}


- (void)testSelectionChanged: (NSArray *)oldSelection
{
   BOOL didChange = NO;
   if (!oldSelection) {
      didChange = YES;
   } else {
      NSArray *newSelection = [self selectedIndexes];
      didChange = ![oldSelection isEqualToArray:newSelection];
   }

   if (didChange) {
      self target] onSelectionDidChange];
   }
}


- ([[NSArray *)filePathsForIndexes: (NSArray *)indexes
{
   NSMutableArray *filePaths = [NSMutableArray array];

   NSEnumerator *e = [indexes objectEnumerator];
   NSNumber *indexNumber;
   while ((indexNumber = [e nextObject])) {
      NSString *filePath = self target] filePathForIndex:[indexNumber intValue;
      if (filePath) {
         [filePaths addObject:filePath];
      }
   }
   return filePaths;
}


@end // TigerCollectionView (Private)


@implementation TigerCollectionViewItem


- (void)dealloc
{
   [cachedTextColors release];
   cachedTextColors = nil;
   [super dealloc];
}

- (void)awakeFromNib
{
   [self setNextResponder:[self superview]];
}


- (void)drawRect: (NSRect)rect
{
   [super drawRect:rect];

   if ([self isSelected]) {
      if ([self shouldDrawSecondaryHighlight]) {
         [[NSColor grayColor] set];
      } else {
         [[NSColor blueColor] set];
      }
      NSRectFill(rect);
   }

   if (dragTargetType != DragTargetType_None) {
      NSRect dRect = [self bounds];
      if (dragTargetType == DragTargetType_Top) {
         dRect.origin.y = dRect.size.height - 2.0;
      }
      dRect.size.height = 2;
      [[NSColor blackColor] set];
      NSRectFill(dRect);
   }
}


@end // TigerCollectionViewItem


@implementation TigerCollectionViewItem (Private)


- (NSMenu *)menuForEvent:(NSEvent *)event
{
   if (![self isSelected]) {
      self collectionView] itemClicked:self event:event];
   }
   return [[self superview] menuForEvent:event];
}


- ([[NSView *)hitTest: (NSPoint)point
{
   NSView *result = [super hitTest:point];
   if (result && ![result isKindOfClass:[NSButton class]]) {
      return self;
   } else {
      return result;
   }
}


- (void)mouseDown: (NSEvent *)event
{
   if ([self isLeftClickEvent:event]) {
      mouseDownPos = [event locationInWindow];
   }
}


- (void)mouseUp: (NSEvent *)event
{
   if ([self isLeftClickEvent:event]) {
      self collectionView] itemClicked:self event:event];
   }
}


- (void)mouseDragged: ([[NSEvent *)event
{
   if ([self isLeftClickEvent:event]) {
      NSPoint mouseDragPos = [event locationInWindow];
      float distance = PointDistance(mouseDragPos, mouseDownPos);
      if (distance > DRAG_START_DISTANCE) {
         self collectionView] startDrag:event withItem:self];
      }
   }
}


- (BOOL)acceptsFirstResponder
{
   return NO;
}


- (void)setIsSelected: (BOOL)flag
{
   if (isSelected != flag) {
      isSelected = flag;
      [self updateHighlightState];
      [self setNeedsDisplay:YES];
   }
}


- (BOOL)isSelected
{
   return isSelected;
}


- ([[TigerCollectionView *)collectionView
{
   ASSERT(self superview] isKindOfClass:[[[TigerCollectionView class]]);
   return (TigerCollectionView *)[self superview];
}


- (BOOL)isLeftClickEvent: (NSEvent *)event
{
   return [event buttonNumber] == 0 &&
          ([event modifierFlags] & NSControlKeyMask) == 0;
}


- (void)setDragTargetType: (DragTargetType)type
{
   if (dragTargetType != type) {
      dragTargetType = type;
      [self setNeedsDisplay:YES];
   }
}


- (DragTargetType)dragTargetType
{
   return dragTargetType;
}


- (void)updateHighlightState
{
   BOOL isHighlighted = [self isSelected] &&
                        ![self shouldDrawSecondaryHighlight];

   NSEnumerator *e = self subviews] objectEnumerator];
   id subview;
   while ((subview = [e nextObject])) {
      if ([subview isKindOfClass:[[[NSTextField class]]) {
         [self setHighlight:isHighlighted
               forTextField:(NSTextField *)subview];                  
      } else if ([subview respondsToSelector:@selector(setIsSelected:)]) {
         [subview setIsSelected:isHighlighted];
      }
   }
}


- (void)setHighlight: (BOOL)isHighlighted
        forTextField: (NSTextField *)textField
{
   NSNumber *key = [NSNumber numberWithInt:[textField hash]];

   if (!cachedTextColors) {
      cachedTextColors = [[NSMutableDictionary alloc] init];
   }

   if (isHighlighted) {
      if (![cachedTextColors objectForKey:key]) {
         [cachedTextColors setObject:[textField textColor] forKey:key];
         [textField setTextColor:[NSColor whiteColor]];
      }
   } else {
      if ([cachedTextColors objectForKey:key]) {
         [textField setTextColor:[cachedTextColors objectForKey:key]];
         [cachedTextColors removeObjectForKey:key];
      }
   }
}


- (BOOL)shouldDrawSecondaryHighlight
{
   if (self window] isKeyWindow]) {
      return NO;
   } else {
      return YES;
   }
}


@end // [[TigerCollectionViewItem (Private)

