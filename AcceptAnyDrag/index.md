---
layout: page
title: AcceptAnyDrag
---



Hello, I have a View, a subclassed NSTabView in fact that I want to be able to get notified and able to accept all drags, no matter what is on the pasteboard. I've made it register for all standard Drag Types, but I want it to be able to recieve drags of custom types too.

The view's data source will store the contents of the pasteboard for later retrieval.
Thanks for any pointers; I've searched the AppKit docs and can find no way to accept drops without registering for that type. 

EnglaBenny

----

I don't know of a Cocoa way, but see the PasteboardPeeker sample code: http://developer.apple.com/samplecode/PasteboardPeeker/PasteboardPeeker.html

----

Asking the question often sparks new thoughts. So did for me, and I tried out the following code:

    
 - (void)mouseEntered:(NSEvent *)theEvent {
    NSPasteboard *pboard = [NSPasteboard pasteboardWithName:NSDragPboard];
    NSArray *pboardTypes = [pboard types];
    [self registerForDraggedTypes:pboardTypes];
 }


It works and     registerForDraggedTypes: is cumulative, so it's easy to implement but it's still not perfect. Since mouseEntered: is not sent perfectly every time,  there is a small lag. Also, I can't find a way to catch "internal" drags where the source most likely returns NSDragOperationNone (or something like that).

EnglaBenny

––––

If you pass kUTTypeData to registerForDraggedTypes:, you'll get all dragged types, it appears. User:Tempelorg|Tempelorg (User talk:Tempelorg|talk) 10:02, 13 January 2013 (EST)

