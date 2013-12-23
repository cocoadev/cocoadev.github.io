---
layout: page
title: PostNotificationWhenSliderEndEditing
---

If you need to have a continuous slider that updates one thing on the fly but has a major process you want done only when the user is done adjusting the slider, implement the following function in a category on NSSliderCell.

    

- (void)stopTracking:(NSPoint)lastPoint at:(NSPoint)stopPoint 
    inView:(NSView *)controlView mouseIsUp:(BOOL)flag
{

    if (flag == YES) {
        NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
        [nc postNotificationName:@"SliderEndEditing"
                          object:[self controlView]];
    }

    [super stopTracking:lastPoint at:stopPoint 
                 inView:controlView mouseIsUp:flag];
}


-Gorman

*Does this count as a tutorial? It seems kinda small :)*
Agreed. But it's not really a discussion either. Just thought it might be useful to somone so stuck it in somehwere. Feel free to delete it.

----

AFAIK, this isn't a smart thing to do. The problem is you are **replacing** API with a category. You are, in effect, telling the ObjC runtime to use your version of -stopTracking:at:inView:mouseIsUp: instead of Apple's version. "super" here doesn't mean "NSSliderCell", it means "NSActionCell".

Here's a test program that demonstrates this point. Note that when you run the program you'll get a runtime exception because "super" doesn't refer to the original version of the class we're modifying:

    
#import <Foundation/Foundation.h>

@interface Foo : NSObject
{
}
- (void)doWork;
@end
@implementation Foo
- (void)doWork
{
    NSLog(@"foo doWork");
}
@end

@interface Foo (Bar_category)
- (void)doWork;
@end
@implementation Foo (bar_category)
- (void)doWork
{
    NSLog(@"bar doWork");
    [super doWork];
}
@end

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    Foo *foo = Foo alloc] init];

    [foo doWork];

    // insert code here...
    [[NSLog(@"Hello, World!");
    [pool release];
    return 0;
}


When I run it, I get this:

    
bar doWork
*** -[Foo doWork]: selector not recognized
*** Uncaught exception: <NSInvalidArgumentException> 
*** -[Foo doWork]: selector not recognized


Why is this dangerous? Since you don't have source for NSSliderCell, you don't know what code you've just replaced. Is it critical for the operation of the object? I guess it doesn't look like it now, but perhaps subtle runtime problems will start to appear. Or your app may break during the next OS release. 

Of course, you won't have this problem if you simply subclass NSSliderCell. "super" will work properly, your modified sliders will be distinguishable from normal sliders, and so on. Subclassing is the proper thing to do here, not using a category. I would go so far as to say that while there are a few distinct cases where CategoriesAreGood, most of the time CategoriesAreBad. I liken them to C's "goto" statement. I use them all the time to do the one thing they're good for, and acknowledge the rest of the time they are bad.

Here's a sample header

    

#import <AppKit/AppKit.h>

#define kNotifySliderCellDidEndEditing @"NotifySliderCellDidEndEditing"

@interface NotifySliderCell : NSSliderCell
@end


Here's the implementation:

    
#import "NotifySliderCell.h"

@implementation NotifySliderCell

- (void)stopTracking:(NSPoint)lastPoint at:(NSPoint)stopPoint 
    inView:(NSView *)controlView mouseIsUp:(BOOL)flag
{

    if (flag == YES) {
        NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
        [nc postNotificationName:kNotifySliderCellDidEndEditing
                          object:[self controlView]];
    }

    [super stopTracking:lastPoint at:stopPoint 
                 inView:controlView mouseIsUp:flag];
}


In order to use this you need to set your slider's cell somewhere early on. You use -[NSControl setCell:] to do that.

Another approach, one I would be more likely to use, would be to send a special message, like a delegate message, to the cell's target. This target is the same object that is notified when the value changes in general, and is often interested in both intermediate and final results. So rather than post a notification, I would see if the target responds to my special selector, and send the message if true. I'll leave that variant as an exercise...

-- MikeTrent

