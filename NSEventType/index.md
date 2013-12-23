---
layout: page
title: NSEventType
---

The enumerator that is used to mask NSEvent types. These correspond to user actions, like clicking, dragging hitting a key, using a scroll wheel, and even custom events that your application "makes up" (NSApplicationDefined).

    
NSEventType type = [NSEvent type];


See Apple's docs at http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSEvent.html .

Values (taken from Apple's AppKit Types and Constants page):
    
typedef enum _NSEventType {
   NSLeftMouseDown = 1,
   NSLeftMouseUp = 2,
   NSRightMouseDown = 3,
   NSRightMouseUp = 4,
   NSMouseMoved = 5,
   NSLeftMouseDragged = 6,
   NSRightMouseDragged = 7,
   NSMouseEntered = 8,
   NSMouseExited = 9,
   NSKeyDown = 10,
   NSKeyUp = 11,
   NSFlagsChanged = 12,
   NSAppKitDefined = 13,
   NSSystemDefined = 14,
   NSApplicationDefined = 15,
   NSPeriodic = 16,
   NSCursorUpdate = 17, 
   NSScrollWheel = 22,
   NSOtherMouseDown = 25,
   NSOtherMouseUp = 26,
   NSOtherMouseDragged = 27
} NSEventType;

