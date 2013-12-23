---
layout: page
title: ReleasingNSOpenGLContext
---

I am having a memory leak associated with an NSOpenGLView subclass. This is a document based application and each document window has an NSOpenGLView. When a document window closes, the NSOpenGLView is deallocated, but the NSOpenGLContext is not released. I tried to call     [NSOpenGLContext clearCurrentContext] to release the current context, but this does not do anything.

Also, each NSOpenGLView generates textures using     glGenTextures() and deletes these textures using     glDeleteTextures(). Is there any other cleanup I need to do before I dealloc my subclass?

----

This is a very odd situation, and I hope you figure it out and explain how, since I'm using OpenGL for my simulation visualization and I may going document-centric in the future.

My immediate hunch is that the document controller or window controller is keeping the window around for re-use possibly by future documents. Or maybe keeping the Nib around for the same reason. Your context may retained because of this. My experience with document based apps is that Cocoa does a LOT of black magic behind the curtain which isn't really explained in the docs.

--ShamylZakariya

----

Well the view is being released when the window closes, so why would the context be retained when the NSOpenGLView that created it is released? If a window controller is keeping the context, wouldn't it stand to reason that the window controller also keep the window around?

----

OK, I just got finished posing NSOpenGLContext and found out the following. Each context is getting allocated and initialized with a retain count of     1. Somewhere the application is retaining the context automatically after the view is created (retainCount is raised to     2) and the application is releasing the context after the window is closed (retainCount is lowered back down to     1). So here's what I'm wondering. Since NSOpenGLView is creating the context automatically when     openGLContext is called, the instance of NSOpenGLView should be releasing the context when it receives the message to deallocate. Right?


----

OK, I finially figured it out. You shouldn't let every document window create it's own NSOpenGLContext because all of the settings (e.g. pixel format) for the documents glView will be the same. NSOpenGLView gives you the chance to share a context across all glViews with identical environments. 

You should make a custom subclass of NSOpenGLView and add global variable for the shared context and shared pixel format

** CustomOpenGLView.h**
    
@interface CustomOpenGLView : NSOpenGLView {

}
@end


** CustomOpenGLView.m**
    
NSOpenGLContext *CustomOpenGLViewSharedContext = nil;
NSOpenGLPixelFormat *CustomOpenGLViewSharedPixelFormat = nil;
 
@implementation CustomOpenGLView

- (id)initWithFrame:(NSRect)frame {
    [self setPostsFrameChangedNotifications:YES];
    if (!CustomOpenGLViewSharedContext) {
        NSOpenGLPixelFormatAttribute attr[] =  {NSOpenGLPFADoubleBuffer, NSOpenGLPFAAccelerated,
                                                NSOpenGLPFAColorSize, BITS_PER_PIXEL, 
                                                NSOpenGLPFADepthSize, DEPTH_SIZE, 0};
        CustomOpenGLViewSharedPixelFormat = [[NSOpenGLPixelFormat alloc] initWithAttributes:attr];
        CustomOpenGLViewSharedContext = [[NSOpenGLContext alloc] 
                                                initWithFormat:CustomOpenGLViewSharedPixelFormat 
                                                shareContext:nil];

    }
    self = [super initWithFrame:frameRect pixelFormat:CustomOpenGLViewSharedPixelFormat];
    [self setOpenGLContext:CustomOpenGLViewSharedContext];
    [CustomOpenGLViewSharedContext setView:self];
    [[self openGLContext] makeCurrentContext];
    [self initGL];
    return self;
}
@end

