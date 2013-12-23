---
layout: page
title: DragAndDropWithNSViewSubclass
---



Here's a simple drag and drop example for filenames dragged from the finder --zootbobbalu

**Header**
    
#import <AppKit/AppKit.h>

@interface TestDragAndDropView : NSView {
    NSColor *backgroundColor, *blueColor, *greenColor;
}

@end


**Source**

    
#import "TestDragAndDropView.h"

@implementation TestDragAndDropView

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        NSArray *draggedTypeArray = [NSArray arrayWithObjects:NSFilenamesPboardType, nil];
        [self registerForDraggedTypes:draggedTypeArray];
        
        /*
        
            Since I'm not aware of any documentation explicitly telling
            you not to retain preset colors, I'm going to retain them 
            anyways!!
        
        */
        
        
        blueColor = backgroundColor = [[NSColor blueColor] retain];
        greenColor = [[NSColor greenColor] retain]; 
    }
    return self;
}

- (void)dealloc {
    [blueColor release];
    blueColor = nil;
    [greenColor release];
    greenColor = nil;

    [super dealloc];
}

- (void)drawRect:(NSRect)rect {
    [backgroundColor set];
    NSRectFill([self visibleRect]);
}

- (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender {
    NSPasteboard *pboard = [sender draggingPasteboard];
    NSArray *filenames = [pboard propertyListForType:NSFilenamesPboardType];
    NSLog(@"draggingEntered: filenames: %@", [filenames description]);
    NSArray *supportedFiletypes = [NSArray arrayWithObjects:@"txt", @"rtf", @"html", 
                                                            @"htm", @"pdf", nil];
    int dragOperation = NSDragOperationNone;
    if ([filenames count]) {
    
        /*
            Decide here if you accept the filenames that are dragged into the view.
            If you do accept the dragged filenames then set dragOperation to 
            NSDragOperationCopy:
            
                dragOperation = NSDragOperationCopy;
                
            Here is where you can give some user feedback if the filenames
            are valid files (e.g. change a boarder color or the background color of the view)
                
        */
        
        NSEnumerator *filenameEnum = [filenames objectEnumerator]; 
        NSString *filename;
        dragOperation = NSDragOperationCopy;
        while (filename = [filenameEnum nextObject]) {
            if (![supportedFiletypes containsObject:[filename pathExtension]]) {
                dragOperation = NSDragOperationNone;
                break;
            }
        }
        if (dragOperation == NSDragOperationCopy) backgroundColor = greenColor;
    }       
    [self setNeedsDisplay:YES];
    return dragOperation;
}

-(void)draggingExited:(id <NSDraggingInfo>)sender {
    NSPasteboard *pboard = [sender draggingPasteboard];
    NSArray *filenames = [pboard propertyListForType:NSFilenamesPboardType];
    backgroundColor = blueColor;
    [self setNeedsDisplay:YES];
    NSLog(@"draggingExited: filenames: %@", [filenames description]);
}

- (BOOL)performDragOperation:(id <NSDraggingInfo>)sender {
    NSPasteboard *pboard = [sender draggingPasteboard];
    NSArray *filenames = [pboard propertyListForType:NSFilenamesPboardType];
    BOOL didPerformDragOperation = NO;
    NSLog(@"performDragOperation: filenames: %@", [filenames description]);
    if ([filenames count]) {
        
        /*
            
            Do something here with filenames and 
            decide if a dragging operation was actually performed.
            If an operation was performed set didPerformDragOperation
            to YES:
            
                didPerformDragOperation = YES;
        
        */
    
    }
    backgroundColor = blueColor;
    [self setNeedsDisplay:YES];
    return didPerformDragOperation;
}

-(void)concludeDragOperation:(id <NSDraggingInfo>)sender {
    
    /*
        This method gives you the chance to change any state variables that
        deal with dragAndDrop.

    */

    NSLog(@"concludeDragOperation:");
}

@end


