---
layout: page
title: CCDImageCategory
---

This allows us to pass an NSImage to a cell's drawWithFrame:inView: instead of an NSView.

    
// CCDImageCategory.h
#import <AppKit/AppKit.h>
@interface NSImage (CCDAddition)
- (NSRect)bounds;
- (BOOL)canDraw;
@end


    
// CCDImageCategory.m
#import "CCDImageCategory.h"
@implementation NSImage (CCDAddition)
- (NSRect)bounds
{
    NSRect rect;
    rect.origin = NSZeroPoint;
    rect.size = [self size];
    return rect;
}
- (BOOL)canDraw { return YES; }
@end


----

Careful - you're starting to set up a parallel structure to CocoaDevUsersAdditions.  Probably want to merge the efforts.
----

I thought about it but it came down to the prefix; NS vs CCD. The fact that NS is used and reserved by Apple was enough to convince me to use CCD. I'd suggest changing CocoaDevUsersAdditions prefix but I don't think that would go over well.
---- 

I don't see why we shouldn't change it.  There's a reason now, which is that there are some cocoa-dev classes that aren't additions. (BTW, there is no NS prefix used in the other categories.  Rather they all have the name CocoaDevUsersAdditions.  The filenames have NS in them, but that doesn't matter.)

Perhaps list all the code together, and gradually convert the old stuff to CCD.
----

Now included in CocoaDevUsersAdditions.



This addition is a bit dangerous -- it doesn't work every time. It works fine for me with single-line text cells and image cells, but crashes the application when trying to draw a multi-line text cell. It crashes upon sending addSubview: to the passed NSImage. EnglaBenny

*I don't claim to know why or how addSubview: is being called but of course it'll crash - you need to provide an implementation, or at the very least a simple no-op cover. If you want it to be 100% safe you'd have to provide a cover for every NSView method and that was a bit more than I wanted to do. Anyway, if/when it crashes it'll tell you what method(s) you need to implement and if you're a kind soul you'll add it here as well so others don't run into the same. :)*


It's not that easy-- to just patch up a category and make it work. I couldn't fix it by adding an addSubview: method to the category, period -> the drawing routine of NSTextFieldCell creates an own view, adds it to the current focus view and draws; not much to do to coerce that into an image. My temporary workaround is not very good, but it is:

    
@interface CellView : NSView  {
	NSCell *cell;
}
@end

@implementation CellView
-(id)initWithFrame:(NSRect)frame cell:(NSCell *)cell {
	if(self = [super initWithFrame:frame]) {
		self->cell = cell;
	}
	return self;
}

-(void)drawRect:(NSRect)rect {
	[cell drawInteriorWithFrame:[self bounds] inView:self];
}
@end
...
CellView *dummyView = [[[CellView alloc] initWithFrame:frame cell:cell] autorelease];
NSImage *cellImage = [[NSImage alloc] initWithData:[dummyView dataWithPDFInsideRect:frame]];
...



This method is slow, just like discussed in SpeedingUpADrag, but works without having to add the view to any other view/window, which is a good thing. EnglaBenny

----
Something like this wouldn't work?...

    
- (void)addSubview:(id)view
{
        [self lockFocus];
        [view drawRect:[view frame]];
        [self unlockFocus];
}


It's not even trying to do "the right thing" but maybe that doesn't matter or could be fixed if it does?

I've tried and I've tried -- but it's not the NSImage that gets called addSubview:. I've hanged around in gdb, and found out that when it calls addSubview: , there is a NSImageCacheView in register 3 (r3), which I think means self, that is the view recieving the message is of that class. But this suddenly went from a small hack to a huge hack, having to class-dump AppKit for interfaces, run gdb and po register addresses etc.. EnglaBenny. 


----
Now there's been too much of my rant on this page; however I think I solved my problems, in a too simple way, it's so easy it's not funny anymore; Simply pass the cell the view +[NSView focusView] to draw in when having locked on the image!

    
[cellImage lockFocus];
[cell drawInteriorWithFrame:frame inView:[NSView focusView]];
[cellImage unlockFocus];

This seems to solve all my problems and results in large speedups, even for drawing text cells. EnglaBenny

*Sweet! Glad it turned out to be so simple.*

note that the category is not needed anymore if you use [cell drawInteriorWithFrame:frame inView:[NSView focusView]];

----
Removed from CocoaDevUsersAdditions and hopefully fixed anything that was using this.

