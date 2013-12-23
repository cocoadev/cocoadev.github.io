---
layout: page
title: CCDPTextView
---

Required for CCDTextField (again).

Used for getting the     selectedRange when the field editor returns garbage.

    
// CCDPTextView.h
#import <AppKit/AppKit.h>

@interface CCDPTextView : NSTextView {}
- (NSRange)prvtSelectedRange;
@end


    
// CCDPTextView.m
#import "CCDPTextView.h"

// This kinda sucks but it's not too terrible.
@implementation CCDPTextView

static NSRange myPrvtSelectedRange;

- (void)setSelectedRange:(NSRange)aRange
{
    myPrvtSelectedRange = aRange;
    [super setSelectedRange:aRange];
}

- (NSRange)prvtSelectedRange
{
    return myPrvtSelectedRange;
}
@end



You will need to modify your main.m file to look like this...
    
#import <Cocoa/Cocoa.h>
#import "CCDPTextView.h"

int main(int argc, const char *argv[])
{
    [CCDPTextView poseAsClass:[NSTextView class]];
    return NSApplicationMain(argc, argv);
}

