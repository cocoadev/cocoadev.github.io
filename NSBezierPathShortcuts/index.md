---
layout: page
title: NSBezierPathShortcuts
---



class methods:

    

+(void)fillRect:(NSRect)rect
+(void)strokeRect:(NSRect)rect
+(void)clipRect:(NSRect)rect
+(void)strokeLineFromPoint:(NSPoint)point1 toPoint:(NSPoint)point2



if speed is critical there here are some C functions you can call directly:

    

void NSRectFill(NSRect rect);
void NSRectFillList(const NSRect *rects, int count);
void NSRectFillListWithGrays(const NSRect *rects, const float *grays, int num);
void NSRectFillListWithColors(const NSRect *rects, NSColor **colors, int num);

//drawing outlines

void NSFrameRect(NSRect rect);
void NSFrameRectWithWidth(NSRect rect, float frameWidth);
void NSFrameRectWithWidthUsingOperation(NSRect rect, float frameWidth, NSCompositingOperation op);


