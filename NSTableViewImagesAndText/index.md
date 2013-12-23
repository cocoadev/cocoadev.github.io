---
layout: page
title: NSTableViewImagesAndText
---

I've found this code from Apple's DragAndDropOutlineView example:

(newbie question at bottom) :-)
    
#import <Cocoa/Cocoa.h>

@interface ImageAndTextCell : NSTextFieldCell {
@private
    NSImage	*image;
}

- (void)setImage:(NSImage *)anImage;
- (NSImage *)image;

- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView;
- (NSSize)cellSize;

@end


and:

    
@implementation ImageAndTextCell

- (void)dealloc {
    [image release];
    image = nil;
    [super dealloc];
}

- copyWithZone:(NSZone *)zone {
    ImageAndTextCell *cell = (ImageAndTextCell *)[super copyWithZone:zone];
    cell->image = [image retain];
    return cell;
}

- (void)setImage:(NSImage *)anImage {
    if (anImage != image) {
        [image release];
        image = [anImage retain];
    }
}

- (NSImage *)image {
    return image;
}

- (NSRect)imageFrameForCellFrame:(NSRect)cellFrame {
    if (image != nil) {
        NSRect imageFrame;
        imageFrame.size = [image size];
        imageFrame.origin = cellFrame.origin;
        imageFrame.origin.x += 3;
        imageFrame.origin.y += ceil((cellFrame.size.height - imageFrame.size.height) / 2);
        return imageFrame;
    }
    else
        return NSZeroRect;
}

- (void)editWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject event:(NSEvent *)theEvent {
    NSRect textFrame, imageFrame;
    NSDivideRect (aRect, &imageFrame, &textFrame, 3 + [image size].width, NSMinXEdge);
    [super editWithFrame: textFrame inView: controlView editor:textObj delegate:anObject event: theEvent];
}

- (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength {
    NSRect textFrame, imageFrame;
    NSDivideRect (aRect, &imageFrame, &textFrame, 3 + [image size].width, NSMinXEdge);
    [super selectWithFrame: textFrame inView: controlView editor:textObj delegate:anObject start:selStart length:selLength];
}

- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView {
    if (image != nil) {
        NSSize	imageSize;
        NSRect	imageFrame;

        imageSize = [image size];
        NSDivideRect(cellFrame, &imageFrame, &cellFrame, 3 + imageSize.width, NSMinXEdge);
        if ([self drawsBackground]) {
            self backgroundColor] set];
            [[NSRectFill(imageFrame);
        }
        imageFrame.origin.x += 3;
        imageFrame.size = imageSize;

        if ([controlView isFlipped])
            imageFrame.origin.y += ceil((cellFrame.size.height + imageFrame.size.height) / 2);
        else
            imageFrame.origin.y += ceil((cellFrame.size.height - imageFrame.size.height) / 2);

        [image compositeToPoint:imageFrame.origin operation:NSCompositeSourceOver];
    }
    [super drawWithFrame:cellFrame inView:controlView];
}

- (NSSize)cellSize {
    NSSize cellSize = [super cellSize];
    cellSize.width += (image ? [image size].width : 0) + 3;
    return cellSize;
}

@end


My question is this: how exactly do I implement this? Right now, I've got another class that has only a few attributes and accessor methods. But I can't figure out how the code above would work with my custom class (I'm assuming I might make an instance of the above in my class?). 

If anyone could enlighten me that would be swell...
----

I'm going to answer my own question... But, feel free to critique what I've got here; I'm sure there must be a better way.

Anyways, all you really need to do, is implement these 2 sets of code:
    
// add this to your - (void) awakFromNib method
NSTableColumn *col = [self tableColumnWithIdentifier:@"whatever"]; 
[col setDataCell:[[[ImageAndTextCell alloc] init] autorelease]]; 

    
id cell; // implement this in your datasource 
cell = [aTableColumn dataCell];
[cell setImage:[NSImage imageNamed:@"OAHelpIcon.tiff"]];


But, make sure you import the previous code too...

- JohnDevor

----

ok it's work fine but ....

lose the setting font for cell text 

ideas ?

- moore
----

I was recently playing around on my own with this, and the way I did it was slightly different.  I similarly made the Image<nowiki/>And<nowiki/>Text<nowiki/>Cell subclass, but instead of having my own setImage, I made an Image<nowiki/>And<nowiki/>Text<nowiki/>Info class (as a subclass of NSObject), which encapsulates an image and a title.  That way, i can just return an Image<nowiki/>And<nowiki/>Text<nowiki/>Info instance in my data source -tableView:objectValueForTableColumn:row: like normal and have my cell access it by self objectValue] image] or [[self objectValue] title].  I also overrode the [[NSCell<nowiki/>s -image and -title accessors to return those values. It simplifies things a bit in my opinion.

-- BrianMoore

----
Have a little example ?
TKS !

-- Toni Moore

