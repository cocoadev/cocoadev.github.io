---
layout: page
title: QuickLook
---

I've done a write-up on how to use Quick Look in your app, including source code: http://ciaranwal.sh/2007/12/07/quick-look-apis -CiaranWalsh


== How to generate QuickLook previews in your application ==

Use the function QLThumbnailImageCreate.

NB: due to it's blocking nature, it is important to call QLThumbnailImageCreate off the main thread however, it shouldn't be called concurrently. The concurrency libraries currently available will attempt to dispatch a second call whilst the first is blocking; this results in a backlog of blocked threads.


== How to call QuickLook panel from your application ==

Apple public APIs allow developers to create QuickLook plugins, but there's no public method to call QuickLook panel. Here's some sample code to achieve this in a Cocoa application:


    
 + (void)openQuickLookPanel:(NSString*)path;
 {		
 	QLPreviewPanel sharedPreviewPanel] setURLs:[NSArray arrayWithObject:[NSURL fileURLWithPath:path currentIndex:0 preservingDisplayState:YES];
 	
 	QLPreviewPanel sharedPreviewPanel] makeKeyAndOrderFrontWithEffect:1];   // 1 = fade in 
 }


You'll have to create and include the following private header to be able to compile:


    
 @class QLAnimationWindowEffect;
 
 @interface QLPreviewPanelReserved : NSObject
 {
     BOOL ignoreOpenAndClose;
 }
 
 - (BOOL)ignoreOpenAndClose;
 - (void)setIgnoreOpenAndClose:(BOOL)fp8;
 
 @end
 
 
 @interface QLPreviewPanel : NSPanel
 {
     QLAnimationWindowEffect *_currentEffect;
     int _openingEffect;
     BOOL _ignorePanelFrameChanges;
     QLPreviewPanelReserved *_reserved;
     double _openingTime;
 }
 
 + (id)sharedPreviewPanel;
 + (id)_previewPanel;
 + (BOOL)isSharedPreviewPanelLoaded;
 - (id)initWithContentRect:(struct _NSRect)fp8 styleMask:(unsigned int)fp24 backing:(unsigned int)fp28 defer:(BOOL)fp32;
 - (id)initWithCoder:(id)fp8;
 - (void)dealloc;
 - (BOOL)isOpaque;
 - (BOOL)canBecomeKeyWindow;
 - (BOOL)canBecomeMainWindow;
 - (BOOL)shouldIgnorePanelFrameChanges;
 - (BOOL)isOpen;
 - (void)setFrame:(struct _NSRect)fp8 display:(BOOL)fp24 animate:(BOOL)fp28;
 - (id)_subEffectsForWindow:(id)fp8 itemFrame:(struct _NSRect)fp12 transitionWindow:(id *)fp28;
 - (id)_scaleEffectForItemFrame:(struct _NSRect)fp8 transitionWindow:(id *)fp24;
 - (void)_invertCurrentEffect;
 - (struct _NSRect)_currentItemFrame;
 - (void)setAutosizesAndCenters:(BOOL)fp8;
 - (BOOL)autosizesAndCenters;
 - (void)makeKeyAndOrderFront:(id)fp8;
 - (void)makeKeyAndOrderFrontWithEffect:(int)fp8;
 - (void)makeKeyAndGoFullscreenWithEffect:(int)fp8;
 - (void)makeKeyAndOrderFrontWithEffect:(int)fp8 canClose:(BOOL)fp12;
 - (void)_makeKeyAndOrderFrontWithEffect:(int)fp8 canClose:(BOOL)fp12 willOpen:(BOOL)fp16 toFullscreen:(BOOL)fp20;
 - (int)openingEffect;
 - (void)closePanel;
 - (void)close;
 - (void)closeWithEffect:(int)fp8;
 - (void)closeWithEffect:(int)fp8 canReopen:(BOOL)fp12;
 - (void)_closeWithEffect:(int)fp8 canReopen:(BOOL)fp12;
 - (void)windowEffectDidTerminate:(id)fp8;
 - (void)_close:(id)fp8;
 - (void)sendEvent:(id)fp8;
 - (void)selectNextItem;
 - (void)selectPreviousItem;
 - (void)setURLs:(id)fp8 currentIndex:(unsigned int)fp12 preservingDisplayState:(BOOL)fp16;
 - (void)setURLs:(id)fp8 preservingDisplayState:(BOOL)fp12;
 - (void)setURLs:(id)fp8;
 - (id)[[URLs;
 - (unsigned int)indexOfCurrentURL;
 - (void)setIndexOfCurrentURL:(unsigned int)fp8;
 - (void)setDelegate:(id)fp8;
 - (id)sharedPreviewView;
 - (void)setSharedPreviewView:(id)fp8;
 - (void)setCyclesSelection:(BOOL)fp8;
 - (BOOL)cyclesSelection;
 - (void)setShowsAddToiPhotoButton:(BOOL)fp8;
 - (BOOL)showsAddToiPhotoButton;
 - (void)setShowsiChatTheaterButton:(BOOL)fp8;
 - (BOOL)showsiChatTheaterButton;
 - (void)setShowsFullscreenButton:(BOOL)fp8;
 - (BOOL)showsFullscreenButton;
 - (void)setShowsIndexSheetButton:(BOOL)fp8;
 - (BOOL)showsIndexSheetButton;
 - (void)setAutostarts:(BOOL)fp8;
 - (BOOL)autostarts;
 - (void)setPlaysDuringPanelAnimation:(BOOL)fp8;
 - (BOOL)playsDuringPanelAnimation;
 - (void)setDeferredLoading:(BOOL)fp8;
 - (BOOL)deferredLoading;
 - (void)setEnableDragNDrop:(BOOL)fp8;
 - (BOOL)enableDragNDrop;
 - (void)start:(id)fp8;
 - (void)stop:(id)fp8;
 - (void)setShowsIndexSheet:(BOOL)fp8;
 - (BOOL)showsIndexSheet;
 - (void)setShareWithiChat:(BOOL)fp8;
 - (BOOL)shareWithiChat;
 - (void)setPlaysSlideShow:(BOOL)fp8;
 - (BOOL)playsSlideShow;
 - (void)setIsFullscreen:(BOOL)fp8;
 - (BOOL)isFullscreen;
 - (void)setMandatoryClient:(id)fp8;
 - (id)mandatoryClient;
 - (void)setForcedContentTypeUTI:(id)fp8;
 - (id)forcedContentTypeUTI;
 - (void)setDocumentURLs:(id)fp8;
 - (void)setDocumentURLs:(id)fp8 preservingDisplayState:(BOOL)fp12;
 - (void)setDocumentURLs:(id)fp8 itemFrame:(struct _NSRect)fp12;
 - (void)setURLs:(id)fp8 itemFrame:(struct _NSRect)fp12;
 - (void)setAutoSizeAndCenterOnScreen:(BOOL)fp8;
 - (void)setShowsAddToiPhoto:(BOOL)fp8;
 - (void)setShowsiChatTheater:(BOOL)fp8;
 - (void)setShowsFullscreen:(BOOL)fp8;
 
 @end



To add the zoom-in animation used by Finder you need to set up a delegate:

    
 // set delegate like this:
 [QLPreviewPanel sharedPreviewPanel] delegate] setDelegate:delegate];
 
 // implement this in delegate
 - (NSRect)previewPanel:(NSPanel*)panel frameForURL:(NSURL*)URL 
 {
    return [self myMethodToGetFrame:URL];
 }


It should return the source screen rectangle for the given URL. Then you just have to change your makeKeyAndOrderFrontWithEffect: call to use 2 and [[QuickLook will take care of everything else - it will automatically request the frame for the current item when opening/closing and then zoom to/from it.  (Tip courtesy of Ciarán Walsh http://ciaranwal.sh/ ) 


On the side note, there's also a command line tool to call QuickLook panel - qlmanage .  http://www.tuaw.com/2007/11/05/terminal-tip-use-quick-look-from-the-leopard-command-line/

--
Cocoatech  
http://www.cocoatech.com

----

I also had to add the Framework found at     /System/Library/PrivateFrameworks/QuickLookUI.framework to make the QLPreviewPanel class available -CiaranWalsh

----

I'd recommend not linking directly to the framework referenced above. Use NSBundle to load it or utilize weak linking. If Apple decides to remove or rename the framework, your app will die at runtime from a dyld error. -rtmfd


== QuickLook generators ==

One important tidbit about implementing QuickLook in your app is that if your document is bundled, you may not even need to write a generator at all. According to the docs:

*If you want to specify static thumbnail and preview images for a bundled document, you can take the easiest approach—it doesn’t even require a generator. Just have your application place the images inside the document bundle in a subfolder named QuickLook; the image file for thumbnails should be named Thumbnail.ext and the file for previews should be named Preview.ext (where ext is an extension such as tiff, png, or jpg). If you decide on this approach, you should not create a generator.*

I'm adding that here because it's buried deep in the docs and is easily overlooked. Seems like a good argument for creating bundled docs! --GC

----

Here is a useful NSImage category that adds the following method:
    
 +(NSImage *)imageWithPreviewOfFileAtPath:(NSString *)path ofSize:(NSSize)size asIcon:(BOOL)icon;

See http://mattgemmell.com/2007/10/29/how-to-get-a-quick-look-preview-as-an-nsimage


----
I'm wondering what restrictions QuickLook has. According to Apple's 300 Feature Page, QuickLook is "sandboxed". Does anybody know what can not be done with QuickLook? Can it access the network / internet?

----
From my, very limited, research, it appears network access is indeed blocked from QuickLook generators.     -stringWithContentsOfURL:encoding:error: called from a QL-generator returns nil for internet NSURLs, though file:// urls work just fine. Also, when using kUTTypeHTML, <img ... /> can only be used when the images are included in a separate CFDictionary, but backgrounds in css are working with normal file://-urls, but that might be a bug... I wonder why they did this though, in what way are QuickLook generators more dangerous than normal apps?
And I should add, that writing local files seems to work...

----

I've seen the following category in the QuickLook framework, the location (NSView) seems odd though.

    
 @interface NSView (QLNSViewAddition)
 - (id)_ql_enclosingPreviewView;
 - (BOOL)_ql_allowsNetworkAccessForPreview:(struct __QLPreview *)fp8;
 - (BOOL)_ql_allowsJavascriptForPreview:(struct __QLPreview *)fp8;
 - (BOOL)_ql_allowsPlugInsForPreview:(struct __QLPreview *)fp8;
 - (BOOL)_ql_allowsFileAccessForPreview:(struct __QLPreview *)fp8;
 - (BOOL)_ql_validateURLSecureAccess:(id)fp8 forPreview:(struct __QLPreview *)fp12;
 @end


----

There are a bunch of undocumented property keys to control the features of the embedded WebView which is used for the preview:

    
 AllowNetworkAccess
 AllowPlugIns
 AllowFullFileAccess
 AllowJavascript


By default all of those features are disabled. You can for example allow plugins in the preview by setting the property like this:

    
 [properties setObject:(id)kCFBooleanTrue forKey:@"AllowPlugIns"];


That would allow you to embed Quicktime or Flash in the HTML you're generating for the preview.

----
I want to place a button on the quicklook panel for my custom file type. It must be possible since betterzip quicklook plugin is doing this, However I find it difficult, even after I grabbed the shared preview panel and modify it, it works only in debug mode, when I run it, glmanager complains that no preview is generated, and requests its thumbnail as a preview. 
Does anyone know how to solve it ?

----
I would like to prevent my quicklook thumbnail from having the page curl and shadow decoration. Have not found a way to prevent this.

----
Try putting a bool entry named QLDisableIconCorners in your Info.plist

----

The APIs are public in Snow Leopard and available in QuickLookUI.framework in Quartz.framework.

----
Got a problem on Snow Leopard with QLThumbnailImageCreate method.
The documentation says that it should return NULL if no thumbnail is available.. That's what it does on Leopard with a missing album artwork on an audio file. But on Snow Leopard, it returns the Apple default "no-artwork" image... which is quite pixellised!
Any clue?

