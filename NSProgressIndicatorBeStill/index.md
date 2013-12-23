---
layout: page
title: NSProgressIndicatorBeStill
---

Is there a way to get a determinate NSProgressIndicator to not animate when it's still, i.e. to look like the iDisk pref pane:

http://www.apple.com/macosx/features/idisk/images/idisk100303.jpg ?

I'm pretty sure thats a custom subclass of it. Might be worth mucking around there, otherwise, make your own widget. Pop open the nib file associated with that pane and see what they have created there, whether it is an NSProgressIndicator or not.

----

I found some infos about this on http://cocoa.mamasam.com/ 
You won't get a "clean" beam like the one shown above (I guess they wrote their own class for this,
but you'll get the exact same NSProgressIndicator, only 100\\% still.

Simply derive a class from NSProgressIndicator, and override the following methods : 
    
@interface NBStonedProgressIndicator : NSProgressIndicator {

}
- (void)startAnimation:(id)sender;
- (void)stopAnimation:(id)sender;
- (void)animate:(id)sender;
@end

@implementation NBStonedProgressIndicator
- (void)startAnimation:(id)sender 
{ 
} 

- (void)stopAnimation:(id)sender 
{ 
} 

- (void)animate:(id)sender 
{ 
} 
@end


I guess you could add your own start/stop flags (can't use the private fields of NSProgressIndicator), and
depending on their values, call      [super animate: sender]; 
I haven't tried this, though.

Another person suggested that if you wanted a clean blue beam, you could write your own NSView subclass, and
draw a widget that's only available with Carbon. (I can't remember the name of that widget).

Hope this helps ^_^

Nico...

----

Seeing as they have images files for the progress indicator in the pref pane, I think that they simply composite the image out to the quota of the iDisk's used space. Then they simply draw the tick marks and labels. I may try throwing together a view that does just that.

----
I quick check with *otool -o -V ...* and/or *class-dump* reveals that this is exactly what they do. It's a straight NSView subclass.

