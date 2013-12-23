---
layout: page
title: NSObjectsDefaultBindImplementation
---



I'm doing some Cocoa programming with CocoaBindings and I get slightly confused when I try to create a custom controller (view) with bindings.


I have a custom NSImageView that I call P<nowiki/>refImageView. The P<nowiki/>refImageView class exposes a binding named "cutting" in the initialize method and has KeyValueCoding compliant accessor methods for the member. P<nowiki/>refImageView differs from a regular NSImageView in that images are always scaled to fill the whole the image view, but the cutting member determines if the image should be stretched disproportionally or have edges cut off if the image and the image view have different proportions.

    PrefImageView.h
- (void) setCutting:(BOOL)b;
- (BOOL) isCutting;

PrefImageView.m
+ (void) initialize {
	[self exposeBinding:@"cutting"];
}

Assume I have a S<nowiki/>witch in Interface builder that I have already bound to the user defaults key @"C<nowiki/>utImage". Now, I wanted to also bind the "cutting" member of my P<nowiki/>refImageView to the same user defaults key, which leads to my two questions regarding bindings:

Question 1: How do I make the bindings of a custom view appear in Interface Builder? In this case it's the "cutting" binding of P<nowiki/>refImageView?

----
Add a call +exposeBinding in [P<nowiki/>refImageView +initialize]:
    
+ (void)initialize {
    [self exposeBinding:@"cutting"];
}


you may also want to add a valueClassForBinding implementation to P<nowiki/>refImageView:
    
- (Class)valueClassForBinding:(NSString *)binding {
    if ([binding isEqualToString:@"cutting"]) {
        return [NSNumber class];
    } else {
        return [super valueClassForBinding:binding];
    }
}


This will allow IB to customize what information it exposes about your binding a little bit such as what value transformers it displays.

DaveMacLachlan
 ----

Since I couldn't find the answer to question 1 anywhere, I tried to programmatically bind "cutting" to the user defaults:
    
[imageView bind:@"cutting" toObject:ud withKeyPath:@"CutImage" options:nil];

This worked fine! Manipulation of the S<nowiki/>witch changes both the user defaults and the visuals of the image view. However, according to all the binding docs I've read, it's not supposed to work, because I'm supposed to have to override the bind method to make anything work. 

Question 2: What exactly does bind:toObject:withKeyPath:options in NSObject do? Can I reliably get away with not overriding bind if I stick to simple KeyValueCoding compliant accessor members?

... I have since added and bound a second member, framed, to P<nowiki/>refImageView and it still works. I have also realised that I could have used NSObjectController in InterfaceBuilder to avoid binding manually.

