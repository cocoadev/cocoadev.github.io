---
layout: page
title: AudioUnitEditorWindow
---

Hey,
I've been using this class for a while, and thought that someone might find this useful. It is a hack up if various sample code from a long time ago, but I gave it the once over, and it works.

- Jeremy Jurksztowicz

    
#import <AppKit/AppKit.h>
#include <AudioUnit/AudioUnit.h>

// ============================================================================================
//
@interface MYAudioUnitEditor : NSWindowController
{
	ComponentDescription _editUnitCD;
	AudioUnit _editUnit;
	AudioUnitCarbonView _editView;
	WindowRef _carbonWindow;
	NSWindow * _cocoaWindow;
	NSSize _defaultViewSize;
	id _delegate;
}

// --------------------------------------------------------------------------------------------

+ (MYAudioUnitEditor*) editorForAudioUnit:(AudioUnit)unit 
	forceGeneric:(BOOL)forceGeneric delegate:(id)delegate;

- (id) initWithAudioUnit:(AudioUnit)unit forceGeneric:(BOOL)forceGeneric delegate:(id)delegate;

- (WindowRef) carbonWindow;

- (AudioUnit) editedUnit;

@end

// ============================================================================================

@interface NSObject (MYAudioUnitEditorDelegate)
- (void) audioUnitEditorClosed:(MYAudioUnitEditor*)auEditor;
@end


Implementation:

    
#import "MYAudioUnitEditor.h"
#import <AudioUnit/AUCocoaUIView.h>

// ============================================================================================
// Carbon implementation from code at the old ucsb audio tutorial website by Chris Reed (no longer up).

@interface MYAudioUnitEditor(MYAudioUnitEditorPrivate)
- (void) _editWindowClosed;
- (BOOL) _error:(NSString*)errString status:(OSStatus)err;
@end

// Carbon event handler.
static OSStatus WindowClosedHandler (
	EventHandlerCallRef myHandler, EventRef theEvent, void* userData)
{
	MYAudioUnitEditor* me = (MYAudioUnitEditor*)userData;
	[me close];
	return noErr;
}

// ============================================================================================
@implementation MYAudioUnitEditor

+ (MYAudioUnitEditor*) editorForAudioUnit:(AudioUnit)unit 
	forceGeneric:(BOOL)forceGeneric delegate:(id)delegate
{
	return [[[MYAudioUnitEditor alloc] initWithAudioUnit:unit 
		forceGeneric:forceGeneric delegate:delegate] autorelease];
}
// --------------------------------------------------------------------------------------------
- (void) _editWindowClosed
{
	// Any additional cocoa cleanup can be added here.
	[_delegate audioUnitEditorClosed:self];
}

- (BOOL) _error:(NSString*)errString status:(OSStatus)err
{
	NSString * errorString = [NSString stringWithFormat:@"%@ failed; %i / %s", 
		errString, err, (char*)&err];
		
	// We just send error to console, do what you will with it.
	NSLog(errorString);
	return NO;
}
// --------------------------------------------------------------------------------------------
- (WindowRef) carbonWindow
{
	return _carbonWindow;
}

- (AudioUnit) editedUnit
{
	return _editUnit;
}
// --------------------------------------------------------------------------------------------
- (BOOL) installWindowCloseHandler
{
	EventTypeSpec eventList[] = { {kEventClassWindow, kEventWindowClose} };	
	EventHandlerUPP	handlerUPP = NewEventHandlerUPP(WindowClosedHandler);
	
	OSStatus err = InstallWindowEventHandler(
		_carbonWindow, handlerUPP, GetEventTypeCount(eventList), eventList, self, NULL);
	if(err != noErr) 
		return [self _error: @"Install close window handler" status: err];
		
	return YES;
}
// --------------------------------------------------------------------------------------------
- (void) initializeEditViewComponentDescription:(BOOL)forceGeneric;
{
	OSStatus err;
	
	// set up to use generic UI component
	_editUnitCD.componentType = kAudioUnitCarbonViewComponentType;
	_editUnitCD.componentSubType = 'gnrc';
	_editUnitCD.componentManufacturer = 'appl';
	_editUnitCD.componentFlags = 0;
	_editUnitCD.componentFlagsMask = 0;
	
	if(forceGeneric) return;
		
	UInt32 propertySize;
	err = AudioUnitGetPropertyInfo(
		_editUnit, kAudioUnitProperty_GetUIComponentList, 
		kAudioUnitScope_Global, 0, &propertySize, NULL);
	
	// An error occured so we will just have to use the generic control.
	if(err != noErr) {
		NSLog(@"Error setting up carbon interface, falling back to generic interface.");
		return;
	}
		
	ComponentDescription *editors = malloc(propertySize);
	err = AudioUnitGetProperty(
		_editUnit, kAudioUnitProperty_GetUIComponentList, kAudioUnitScope_Global,
		0, editors, &propertySize);
		
	if(err == noErr)
		_editUnitCD = editors[0]; // We just pick the first one. Select whatever you like.
		
	free(editors);
}
// --------------------------------------------------------------------------------------------
+ (BOOL) pluginClassIsValid:(Class)pluginClass 
{
	if([pluginClass conformsToProtocol: @protocol(AUCocoaUIBase)]) {
		if([pluginClass instancesRespondToSelector: @selector(interfaceVersion)] &&
		   [pluginClass instancesRespondToSelector: @selector(uiViewForAudioUnit:withSize:)]) {
				return YES;
		}
	}
    return NO;
}

- (BOOL) hasCocoaView
{
	UInt32 dataSize   = 0;
	Boolean isWritable = 0;
	OSStatus err = AudioUnitGetPropertyInfo(_editUnit,
		kAudioUnitProperty_CocoaUI, kAudioUnitScope_Global,
		0, &dataSize, &isWritable);
									
	return dataSize > 0 && err == noErr;
}

- (NSView *) getCocoaView
{
	NSView *theView = nil;
	UInt32 dataSize = 0;
	Boolean isWritable = 0;
	OSStatus err = AudioUnitGetPropertyInfo(_editUnit,
		kAudioUnitProperty_CocoaUI, kAudioUnitScope_Global, 
		0, &dataSize, &isWritable);
									
	if(err != noErr)
		return theView;
									
	// If we have the property, then allocate storage for it.
	AudioUnitCocoaViewInfo * cvi = malloc(dataSize);
	err = AudioUnitGetProperty(_editUnit, 
		kAudioUnitProperty_CocoaUI, kAudioUnitScope_Global, 0, cvi, &dataSize);

	// Extract useful data.
	unsigned numberOfClasses = (dataSize - sizeof(CFURLRef)) / sizeof(CFStringRef);
	NSString * viewClassName = (NSString*)(cvi->mCocoaAUViewClass[0]);
	NSString * path = (NSString*)(CFURLCopyPath(cvi->mCocoaAUViewBundleLocation));
	NSBundle * viewBundle = [NSBundle bundleWithPath:[path autorelease]];
	Class viewClass = [viewBundle classNamed:viewClassName];
	
	if([MYAudioUnitEditor pluginClassIsValid:viewClass]) {
		id factory = [viewClass alloc] init] autorelease];
		theView = [factory uiViewForAudioUnit:_editUnit withSize:_defaultViewSize];
	}
	
	// Delete the cocoa view info stuff.
	if(cvi) {
        int i;
        for(i = 0; i < numberOfClasses; i++)
            [[CFRelease(cvi->mCocoaAUViewClass[i]);
        
        CFRelease(cvi->mCocoaAUViewBundleLocation);
        free(cvi);
    }
	
	return theView;
}
// --------------------------------------------------------------------------------------------
- (BOOL) createCarbonWindow
{
	Component editComponent = FindNextComponent(NULL, &_editUnitCD);
	OpenAComponent(editComponent, &_editView);
	if (!_editView)
		[NSException raise:NSGenericException format:@"Could not open audio unit editor component"];
	
	Rect bounds = { 100, 100, 100, 100 }; // Generic resized later
	OSStatus res = CreateNewWindow(kFloatingWindowClass, 
		kWindowCloseBoxAttribute | kWindowCollapseBoxAttribute | kWindowStandardHandlerAttribute |
		kWindowCompositingAttribute | kWindowSideTitlebarAttribute, &bounds, &_carbonWindow);
		
	if(res != noErr)
		return [self _error:@"Create new carbon window" status:res];
	
	// create the edit view
	ControlRef rootControl;
	res = GetRootControl(_carbonWindow, &rootControl);
	if(!rootControl) 
		return [self _error:@"Get root control of carbon window" status:res];

	ControlRef viewPane;
	Float32Point loc  = { 0.0, 0.0 };
	Float32Point size = { 0.0, 0.0 } ;
	AudioUnitCarbonViewCreate(_editView, _editUnit, _carbonWindow, 
		rootControl, &loc, &size, &viewPane);
		
	// resize and move window
	GetControlBounds(viewPane, &bounds);
	size.x = bounds.right-bounds.left;
	size.y = bounds.bottom-bounds.top;
	SizeWindow(_carbonWindow, (short) (size.x + 0.5), (short) (size.y + 0.5),  true);
	RepositionWindow(_carbonWindow, NULL, kWindowCenterOnMainScreen);
	
	return YES;
}
// --------------------------------------------------------------------------------------------
- (BOOL) createCocoaWindow
{
	if([self hasCocoaView])
	{
		NSView * res = [self getCocoaView];
		if(res) {
			NSWindow * window = [[[NSWindow alloc] initWithContentRect:
				NSMakeRect(100, 400, [res frame].size.width, [res frame].size.height) styleMask:
				NSTitledWindowMask | NSClosableWindowMask | NSMiniaturizableWindowMask | NSResizableWindowMask
				backing:NSBackingStoreBuffered defer:NO] autorelease];
				
			[window setContentView:res];
			[window setIsVisible:YES];
			[self setWindow:window];
			_cocoaWindow = window;
			return YES;
		}
	}
	return NO;
}
// --------------------------------------------------------------------------------------------
- (void) showWindow:(id)sender
{
	if(_cocoaWindow)
		[super showWindow:sender];
	else if(_carbonWindow)
		SelectWindow(_carbonWindow);
}

- (void) close
{
	if(_cocoaWindow) {
		[super close];
		_cocoaWindow = nil;
	}
	else if(_carbonWindow) {
		DisposeWindow(_carbonWindow);
		_carbonWindow = 0;
	}
	[self _editWindowClosed];
}
// --------------------------------------------------------------------------------------------
- (id) initWithAudioUnit: (AudioUnit) unit forceGeneric: (BOOL) forceGeneric delegate: (id) delegate
{
	_editUnit = unit;
	_delegate = delegate;
	_defaultViewSize = NSMakeSize(400, 300);
	
	// We need to chack this in showWindow:
	_carbonWindow = 0;
	
	if([self hasCocoaView]) {
		[self createCocoaWindow];
		self = [super initWithWindow:[_cocoaWindow autorelease]];
	}
	else self = [super initWithWindow:nil];
	
	if(!self) 
		return nil;
	
	// Run only if we did not create a cocoa window.
	// Switch order to make carbon window the default.
	if(!_cocoaWindow) {
		[self initializeEditViewComponentDescription: forceGeneric];
		
		if(![self createCarbonWindow])  {
			[self release];
			return nil;
		}
			
		if(![self installWindowCloseHandler]) {
			[self release];
			return nil;
		}

		// create the cocoa window for the carbon one and make it visible.
		NSWindow *cWindow = [[NSWindow alloc] initWithWindowRef:_carbonWindow];
		[self setWindow:cWindow];
		[cWindow setIsVisible:YES];
	}
	return self;
}

- (void) dealloc
{
	[self setWindow:nil];
	if(_editView)
		CloseComponent(_editView);
	if(_carbonWindow)
		DisposeWindow(_carbonWindow);
		
	[super dealloc];
}

@end

