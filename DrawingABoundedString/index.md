---
layout: page
title: DrawingABoundedString
---




    
- (void)drawString:(NSString *)string inRect:(NSRect)rect {

    static NSDictionary *att = nil;
    if (!att) {
        NSMutableParagraphStyle *style = [[NSParagraphStyle defaultParagraphStyle] mutableCopy];
        [style setLineBreakMode:NSLineBreakByWordWrapping];
        [style setAlignment:NSCenterTextAlignment];
        att = [[NSDictionary alloc] initWithObjectsAndKeys:
                            style, NSParagraphStyleAttributeName, 
                            [NSColor whiteColor], NSForegroundColorAttributeName, nil];
        [style release];
    }
    [string drawInRect:rect withAttributes:att];

}



--zootbobbalu

