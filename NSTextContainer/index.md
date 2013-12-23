---
layout: page
title: NSTextContainer
---

An NSTextContainer defines a region where text is laid out. An NSLayoutManager uses NSTextContainer (s) to determine where to break lines, lay out portions of text, and so on. NSTextContainer defines rectangular regions, but you can create subclasses that define regions of other shapes, such as circular regions, regions with holes in them, or regions that flow alongside graphics.

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTextContainer_Class/index.html#//apple_ref/doc/uid/TP40004132

----

I think of NSTextContainer as a sort of 'scratch pad' for NSLayoutManager  to work in before drawing to a view.

----
Has any one worked on irregular shaped text container??? 
----
The following is a sample that performs text layout and editing within a text container defined by an arbitrary closed convex Bezier path.  The sample is deliberately kept simple and uses a brute force technique.  There are suggestions for improvements in the code.  For a more general solution, mix this sample with arbitrary path filling logic.  The seminal Graphics GEMS series will aid with path filling.  The Opensource libArt project demonstrates an efficient way to handle concave paths, paths with voids (doughnuts), and vastly more efficient convex paths.  libArt conveniently "raterizes" to horizontal rectangles which are exacly what is needed for this sample.

* MyBezierTextContainer.h*
    
@interface MyBezierTextContainer : NSTextContainer 
{
   id          pathSource;
}

- (id)pathSource;
- (void)setPathSource:(id)aPathSource;

@end


* MyBezierTextContainer.m*
    
#import "MyBezierTextContainer.h"


@interface NSObject (MyBezierTextContainerPathSource)

- (NSBezierPath *)closedBezierPathForTextContainer:(id)aContainer;

@end


@implementation MyBezierTextContainer

- (void)dealloc
{
   [self setPathSource: nil];
   [super dealloc];
}

- (id)pathSource
{
   return pathSource; 
}

- (void)setPathSource:(id)aPathSource
{
   [aPathSource retain];
   [pathSource release];
   pathSource = aPathSource;
}

- (BOOL)isSimpleRectangularTextContainer
{
   return (nil != [self pathSource]);
}

- (BOOL)containsPoint:(NSPoint)aPoint
{
   BOOL        result = [super containsPoint:aPoint];
   
   if(result && nil != [self pathSource])
   {
      NSBezierPath *bezierPath = self pathSource] closedBezierPathForTextContainer:self];
      
      result = [bezierPath containsPoint:aPoint];
   }
   
   return result;
}

- ([[NSRect)lineFragmentRectForProposedRect:(NSRect)proposedRect sweepDirection:(NSLineSweepDirection)sweepDirection movementDirection:(NSLineMovementDirection)movementDirection remainingRect:(NSRectPointer)remainingRect
{
   NSRect         result = NSZeroRect;
   
   if(nil == [self pathSource])
   {
      result = [super lineFragmentRectForProposedRect:proposedRect sweepDirection:sweepDirection movementDirection:movementDirection remainingRect:remainingRect];
   }
   else
   {
      NSBezierPath      *bezierPath = self pathSource] closedBezierPathForTextContainer:self];
      
      if(NULL != remainingRect)
      {
         *remainingRect = [[NSZeroRect;
      }
      
      result = NSIntersectionRect(proposedRect, [bezierPath bounds]);
      
      if(result.size.height < proposedRect.size.height)
      {
         result = NSZeroRect;
      }
      else
      {
         NSPoint     topLeft = result.origin;
         NSPoint     topRight = result.origin;
         topRight.x += result.size.width;
         NSPoint     bottomLeft = result.origin;
         bottomLeft.y += result.size.height;
         NSPoint     bottomRight = topRight;
         bottomRight.y += result.size.height;
         
         // Improvement: Use binary search or "quad" tree instead of brute force search
         while(![bezierPath containsPoint:topLeft] && (topLeft.x < topRight.x))
         {
            topLeft.x += 1.0f;
         }
         bottomLeft.x = topLeft.x;
         while(![bezierPath containsPoint:bottomLeft] && (bottomLeft.x < bottomRight.x))
         {
            bottomLeft.x += 1.0f;
         }
         topLeft.x = bottomLeft.x;
         while(![bezierPath containsPoint:topRight] && (topLeft.x < topRight.x))
         {
            topRight.x -= 1.0f;
         }
         bottomRight.x = topRight.x;
         while(![bezierPath containsPoint:bottomRight] && (bottomLeft.x < bottomRight.x))
         {
            bottomRight.x -= 1.0f;
         }
         topRight.x = bottomRight.x;
         
         result = NSMakeRect(topLeft.x, topLeft.y, MAX(1.0f, bottomRight.x - topLeft.x), bottomRight.y - topLeft.y);
         
         if(NSIsEmptyRect(result))
         {
            result = NSZeroRect;
         }
      }
   }
   
   return result;
}

@end


To test the custom text container, create a standard Cocoa multi-document application and add the following code to the document class: (You will want to connect the *textView* outlet to a text view within the document nib.
*MyDocument.h*
    
@class MyBezierTextView;


@interface MyDocument : NSDocument
{
   NSBezierPath                  *bezierPath;
   IBOutlet NSTextView           *textView;
}

- (NSBezierPath *)bezierPath;
- (void)setBezierPath:(NSBezierPath *)aBezierPath;

@end


* MyDocument.m*
    
- (id)init
{
    self = [super init];
    if (self) 
    {
       // Use the following line instead of the complex path made of curves if you want something simple
       //[self setBezierPath:[NSBezierPath bezierPathWithOvalInRect:NSMakeRect(20.0f, 0.0f, 300.0f, 600.0f)]]; 
       
       // Define a complex path using curves and lines
       NSBezierPath     *newPath = [NSBezierPath bezierPath];
       
       srandom((int)[NSDate timeIntervalSinceReferenceDate]);
       [newPath moveToPoint:NSMakePoint(100.0f + fmod(random(), 100.0f), -1.0f)];
       float      curveWidth = 100.0f + fmod(random(), 200.0f);
       float      curveHeight = 100.0f + fmod(random(), 200.0f);
       [newPath relativeCurveToPoint:NSMakePoint(curveWidth, curveHeight) controlPoint1:NSMakePoint(curveWidth, 0.0f) controlPoint2:NSMakePoint(0.0f, curveHeight)];
       [newPath lineToPoint:NSMakePoint(400.0f, 400.0f)];
       [newPath lineToPoint:NSMakePoint(300.0f, 400.0f)];
       curveWidth = -100.0f - fmod(random(), 200.0f);
       curveHeight = -100.0f - fmod(random(), 200.0f);
       [newPath relativeCurveToPoint:NSMakePoint(curveWidth, curveHeight) controlPoint1:NSMakePoint(curveWidth, 0.0f) controlPoint2:NSMakePoint(0.0f, curveHeight)];
       [newPath closePath];
       [self setBezierPath:newPath]; 
    }
    return self;
}

- (void)dealloc
{
   [self setBezierPath: nil];
   [super dealloc];
}

- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    
   // Swap out the IB genertade text view's text container for our custom container
   NSAssert(nil != textView, @"Failed to connect textView in IB");
   MyBezierTextContainer  *textContainer = [[[MyBezierTextContainer alloc] initWithContainerSize:[textView bounds].size] autorelease];
   
   [textContainer setPathSource:self];
   [textContainer setWidthTracksTextView:YES];
   textView layoutManager] addTextContainer:textContainer];
   while(1 < [[[textView layoutManager] textContainers] count])
   {
      [[textView layoutManager] removeTextContainerAtIndex:0];
   }
   
   [textView replaceTextContainer:textContainer];
   
   if([textView respondsToSelector:@selector(setPathSource:)])
   {
      [(id)textView setPathSource:self];
   }
}

- ([[NSBezierPath *)bezierPath
{
   return bezierPath; 
}

- (void)setBezierPath:(NSBezierPath *)aBezierPath
{
   [aBezierPath retain];
   [bezierPath release];
   bezierPath = aBezierPath;
}

- (NSBezierPath *)closedBezierPathForTextContainer:(id)aContainer
{
   return [self bezierPath];
}

@end


If you want to be able to see the path in which the text flows, use the following simple subclass of NSTextView instead of the base NSTextView class:
*MyBezierTextView.h*
    
@interface MyBezierTextView : NSTextView 
{
   id          pathSource;
}

- (id)pathSource;
- (void)setPathSource:(id)aPathSource;

@end


*MyBezierTextView.m*
    
#import "MyBezierTextView.h"


@interface NSObject (MyBezierTextContainerPathSource)

- (NSBezierPath *)closedBezierPathForTextContainer:(id)aContainer;

@end


@implementation MyBezierTextView

- (void)dealloc
{
   [self setPathSource: nil];
   [super dealloc];
}

- (id)pathSource
{
   return pathSource; 
}

- (void)setPathSource:(id)aPathSource
{
   [aPathSource retain];
   [pathSource release];
   pathSource = aPathSource;
}

- (void)drawRect:(NSRect)rect 
{
   [super drawRect:rect];
   [[NSColor blackColor] set];
   [self pathSource] closedBezierPathForTextContainer:self] stroke];
   [[[[NSColor grayColor] set];
   NSFrameRect([[[self pathSource] closedBezierPathForTextContainer:self] bounds]);
}

@end

