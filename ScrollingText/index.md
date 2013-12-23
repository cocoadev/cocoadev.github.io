---
layout: page
title: ScrollingText
---

In a recent project (remaking the iTunes controller window for my brother), I needed to have a view with scrolling text, so that if you shrunk the window, the song title would scroll across it. After much trial and error with NSTextField, I declared it impossible...using those ideas. Instead, I created a new subclass of NSView called (what else) ScrollingText. I didn't choose S<nowiki/>crollingTextView because that sounds like a subclass of NSTextView. It basically works by eating a character for each increment (calling the     scrollOneGlyph method increases the offset), and stopping once the offset goes past the edge of the view. I had to learn about the Cocoa Text System to finish this, basically creating my own NSTextView (that didn't subclass NSText).

Someone might point out that it is more economic to use the     mutableString function of NSTextStorage (really of NSMutableAttributedString). I agree; so if someone figures out why it crashes every time I try, then please update the code accordingly (test your solution first!)

If someone (maybe me) ends up implementing this as an NSText subclass, it would become that much more useful.

--JediKnil

**ScrollingText.h**
    
#import <Cocoa/Cocoa.h>

@interface ScrollingText : NSView {
    NSTextContainer *textContainer;
    NSTextStorage *textStorage;
    NSLayoutManager *layoutManager;
    NSMutableString *string;
	
    unsigned offset;
    BOOL shouldAdd;
}
- (void)setup;
- (NSString *)string;
- (void)setString:(NSString *)newString;
- (void)setString:(NSString *)value resettingScroll:(BOOL)shouldReset;
- (void)scrollOneGlyph;
@end


**ScrollingText.m**
    
#import "ScrollingText.h"

static NSDictionary *defaultAttributes = nil;

@implementation ScrollingText

- (id)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (self != nil) {
        [self setup];
    }
	
    return self;
}

- (BOOL)isFlipped
{
    return YES;
}

- (void)setup
{
    NSMutableParagraphStyle *style;

    textStorage = [[NSTextStorage alloc] init];
    layoutManager = [[NSLayoutManager alloc] init];
    textContainer = [[NSTextContainer alloc] init];
	
    [layoutManager addTextContainer:textContainer];
    [textContainer release];
    [textStorage addLayoutManager:layoutManager];
    [layoutManager release];
	
    style = [[NSMutableParagraphStyle alloc] init];
    [style setAlignment:NSCenterTextAlignment];
    [style setLineBreakMode:NSLineBreakByCharWrapping];
	
    if (defaultAttributes == nil) {
        defaultAttributes = [[NSDictionary alloc] initWithObjectsAndKeys:[NSFont userFixedPitchFontOfSize:0.0], NSFontAttributeName,
                                    [style autorelease], NSParagraphStyleAttributeName,
                                    nil];
    }
		
    [textContainer setContainerSize:[self frame].size];
		
    string = [[NSMutableString alloc] init];
    offset = 0;
}

- (void)drawRect:(NSRect)rect
{
    NSRange glyphRange;
    NSPoint origin = [self bounds].origin;
    NSString *scrollStr;
    unsigned length = [string length];
		
    glyphRange = [layoutManager glyphRangeForTextContainer:textContainer];
	
    if (offset + [layoutManager characterRangeForGlyphRange:glyphRange actualGlyphRange:nil].length >= length) {
        shouldAdd = NO;
    }
    scrollStr = [string substringFromIndex:offset];
	
    [textStorage setAttributedString:[[[NSAttributedString alloc] initWithString:scrollStr attributes:defaultAttributes] autorelease]];
					
    glyphRange = [layoutManager glyphRangeForTextContainer:textContainer];
	
    [layoutManager drawGlyphsForGlyphRange:glyphRange atPoint:origin];
	
    [super drawRect:rect];
}

- (void)viewDidEndLiveResize
{
    [textContainer setContainerSize:[self frame].size];
    [self setNeedsDisplay:YES];
}

- (BOOL)inLiveResize
{
    if ([super inLiveResize]) {
        [textContainer setContainerSize:[self frame].size];
        return YES;
    } else return NO;
}

- (void)setString:(NSString *)value
{
    [self setString:value resettingScroll:YES];
}

- (void)setString:(NSString *)value resettingScroll:(BOOL)shouldReset
{
    if (value == nil || [value isEqualToString:string]) {
        // If the new string is nil OR is the same as the old string...
        // ...just redisplay and return
        [self setNeedsDisplay:YES];
        return;
    }
	
    [string setString:value];
	
    if (shouldReset)
        offset = 0;
	
    [self setNeedsDisplay:YES];
}

- (NSString *)string
{
    return string;
}

- (void)scrollOneGlyph
{
    if (shouldAdd) {
        offset++;
    } else {
        offset = 0;
        shouldAdd = YES;
    }
}

- (void)dealloc
{
    [textStorage release];
    [string release];
    [super dealloc];
}
@end

----
I would render the text once onto an NSImage then only render a peice of it at a time, aka moving the rendering rectangle to the right instead of the text to the left.  That'll save you from using all those costly text functions.

- FranciscoTolmasky

**Yeah, I looked into that, but that's also pretty costly for my purpose: an iTunes controller. Whenever the song information changes, you would have to update the NSImage...which looked worse to me for some reason (maybe you're right). If you come up with a working version of your idea, please post the code up here. --JediKnil**

Writing to a view or to an NSImage is equally costly. - FranciscoTolmasky

**I meant drawing the NSImage to the view *as well as* writing the text to the NSImage. The difference is that the text has to be written to the view every time in my solution, whereas only a pre-rendered image has to be written to the view in yours. So even though I have to keep track of the image as well, you're probably right. --JediKnil**

----

Is it just me or could you just use an NSScrollView, put your view inside that, and then use NSClipView's scrollToPoint: ?

**Again, yeah, but I didn't exactly know how to do that in IB without the scroll bars, etc. Besides, the problem with all of this is it won't always scroll by one character (although this might be considered a feature). Anyway, it was a learning experience for me... --JediKnil**

Thats cool :^), that is what makes programming worth while hehe.

You can do it kind of that way, but you don't need a NSScrollView, just a NSClipView with a NSImageView or NSTextView inside it - Create a NSImage/NSTextView in interface builder and select it, then go to the Layout menu and click Make Subviews Of -> Custom View then select NSClipView as its custom class then use scrollToPoint to move it around!  This approach needs very little code but may not be the most efficient way of doing things :) -- Tim Ellis

----

Why not call it MarqueeView

*Yes, yes, it seems like people are more interested in this than I would have thought (and also much smarter than I am). Maybe we should make this a discussion, or something -- the best way to animate scrolling text. MarqueeView is also probably a better name than ScrollingText. Anyone want to post some more efficient code for the CocoaDev community? --JediKnil*

----

I modified the rect before passing to super in     drawWithFrame:InView: of NSTextFieldCell and got decent results. Nothing I would share (or use) but not terrible for a 5 minute distraction. *shrug*

