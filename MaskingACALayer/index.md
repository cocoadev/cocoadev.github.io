---
layout: page
title: MaskingACALayer
---


I couldn't find any examples of using a mask on a CALayer, so I'm posting one.  
Just create a custom view and put this code in it to see how they work.

Or download the sample project I created and posted on my blog.

http://allusions.sourceforge.net/cocoamondo/?p=3

-- MatthieuCormier

    

- (void)awakeFromNib {    
  [self maskTest]; 
}

- (CGImageRef)createMaskImage {
  [[NSColor colorWithCalibratedWhite:1.0 alpha:1.0] set];
  
  float minX = 0;
  float minY = 0;
  float maxX = 100;
  float maxY = 100;  
  
  NSBezierPath* path = [NSBezierPath bezierPath];
  NSPoint topLeft     = NSMakePoint (minX, maxY);
	NSPoint topRight    = NSMakePoint (maxX, maxY);
	NSPoint bottomRight = NSMakePoint (maxX, minY);
	NSPoint bottomLeft  = NSMakePoint (minX, minY);
  
  NSPoint centerPoint = NSMakePoint((maxX - minX) * 0.5, (maxY - minY) * 0.5);
  
  [path moveToPoint:topLeft];
  [path lineToPoint:topRight];  
  [path curveToPoint:centerPoint controlPoint1:NSMakePoint((maxX - minX) * 0.5, maxY) 
       controlPoint2:centerPoint];  
  [path curveToPoint:bottomRight controlPoint1:NSMakePoint((maxX - minX) * 0.5, minY) 
       controlPoint2:bottomRight];
  [path lineToPoint:bottomLeft];
  
  NSImage* maskImage = [[NSImage alloc] initWithSize:NSMakeSize(100, 100)];
  [maskImage lockFocus];
  {
    [[NSColor colorWithCalibratedWhite:1.0 alpha:1.0] set];
    [path fill];
    [path stroke];
  }
  [maskImage unlockFocus];
    
 
  CGImageSourceRef source;
  
  source = CGImageSourceCreateWithData((CFDataRef)[maskImage TIFFRepresentation], NULL);
  CGImageRef maskRef =  CGImageSourceCreateImageAtIndex(source, 0, NULL);
  
  [maskImage release];
  return maskRef;
  
}



- (void)maskTest {
  CGRect viewFrame = NSRectToCGRect( self.frame );
  viewFrame.origin.y = 0;
  
  // create a layer and match its frame to the view's frame
  self.wantsLayer = YES;
  CALayer* mainLayer = self.layer;
  mainLayer.name = @"mainLayer";
  mainLayer.frame = viewFrame;
  mainLayer.delegate = self;
  
  // causes the layer content to be drawn in -drawRect:
  [mainLayer setNeedsDisplay];
  
  
  CGFloat midX = CGRectGetMidX( mainLayer.frame );
  CGFloat midY = CGRectGetMidY( mainLayer.frame );
  
  // create a "container" layer for all content layers.
  // same frame as the view's master layer, automatically
  // resizes as necessary.    
  CALayer* contentContainer = [CALayer layer];    
  contentContainer.bounds           = mainLayer.bounds;
  contentContainer.delegate         = self;
  contentContainer.anchorPoint      = CGPointMake(0.5,0.5);
  contentContainer.position         = CGPointMake( midX, midY );
  contentContainer.autoresizingMask = kCALayerWidthSizable | kCALayerHeightSizable;
  
  contentContainer.borderWidth = 2.0;
  contentContainer.borderColor = SFWhiteColor;
  
  [self.layer addSublayer:contentContainer];
  
  
  CALayer* boxLayer = [CALayer layer];    
  boxLayer.frame           = CGRectMake(0, 0, 100, 100);
  boxLayer.backgroundColor =  CGColorCreateGenericRGB(0.0f,0.0f,0.0f,1.0f);
  boxLayer.position = CGPointMake(110, 10);
  boxLayer.anchorPoint = CGPointMake(0,0);
  
  
  CALayer* maskLayer = [CALayer layer];    
  maskLayer.frame           = CGRectMake(0, 0, 100, 100);
  maskLayer.position = CGPointMake(0, 0);
  maskLayer.anchorPoint = CGPointMake(0,0);  
  
  CGImageRef maskImage = [self createMaskImage];
  [maskLayer setContents:(id)maskImage];


  [boxLayer setMask:maskLayer];
  
  [contentContainer addSublayer:boxLayer];
  
  [contentContainer layoutSublayers];
  [contentContainer layoutIfNeeded]; 
}


