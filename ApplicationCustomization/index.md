---
layout: page
title: ApplicationCustomization
---

From http://developer.apple.com/documentation/Cocoa/Conceptual/AppCustomization/index.html :

*This topic describes how a Cocoa application can customize its behavior by detecting the operating system version.*

----

Documentation will deal with different versions of OS X and the various features available in each.

Cocoa is full of features that date from the days when Cocoa's predecessor ran on 5 hardware platforms and 3 different operating systems including Windows NT.

http://en.wikipedia.org/wiki/OpenStep

For example, I think - (BOOL)applicationShouldTerminateAfterLastWindowClosed: was originally provided so that Openstep applications could be made to behave like Windows applications.

For another example, look in NSProcessInfo.h

    
enum {	/* Constants returned by -operatingSystem */
	NSWindowsNTOperatingSystem = 1,
	NSWindows95OperatingSystem,
	NSSolarisOperatingSystem,
	NSHPUXOperatingSystem,
	NSMACHOperatingSystem,
	NSSunOSOperatingSystem,
	NSOSF1OperatingSystem
};


or this in NSWindow.h

    
#ifdef WIN32
@interface NSWindow (NSWindowsExtensions)
- (void * /*HWND*/)windowHandle;
@end


or NSMenu.h

    
// These methods are platform specific.  They really make little sense off Windows.  Their use is discouraged.
- (NSMenu *)attachedMenu;
- (BOOL)isAttached;
- (void)sizeToFit;
- (NSPoint)locationForSubmenu:(NSMenu *)aSubmenu;


or NSInterfaceStyle.h

    
typedef enum {
    NSNoInterfaceStyle = 0,    // Default value for a window's interfaceStyle
    NSNextStepInterfaceStyle = 1, 
    NSWindows95InterfaceStyle = 2,
    NSMacintoshInterfaceStyle = 3
} NSInterfaceStyle;

APPKIT_EXTERN NSInterfaceStyle NSInterfaceStyleForKey(NSString *key, NSResponder *responder);
    // Responders can use this function to parameterize their drawing and behavior.  If the responder has specific defaults to control various aspects of its interface individually, the keys for those special settings can be passed in, otherwise pass nil to get the global setting.  The responder should always be passed, but in situations where a responder is not available, pass nil.


or NSImage.h

    
#ifdef WIN32

@interface NSImage (NSWindowsExtensions)
- (id)initWithIconHandle:(void * /* HICON */)icon;
- (id)initWithBitmapHandle:(void * /* HBITMAP */)bitmap;
@end

#endif



or NSApplication.h

    
#ifdef WIN32
@interface NSApplication (NSWindowsExtensions)
+ (void)setApplicationHandle:(void * /*HINSTANCE*/)hInstance previousHandle:(void * /*HINSTANCE*/)PrevInstance commandLine:(NSString *)cmdLine show:(int)cmdShow;
+ (void)useRunningCopyOfApplication;
- (void * /*HINSTANCE*/)applicationHandle;
- (NSWindow *)windowWithWindowHandle:(void * /*HWND*/)hWnd; // does not create a new NSWindow
@end
#endif


or

    
#elif defined(WIN32)

#ifndef _NSBUILDING_APPKIT_DLL
#define _NSWINDOWS_DLL_GOOP	__declspec(dllimport)
#else
#define _NSWINDOWS_DLL_GOOP	__declspec(dllexport)
#endif


or

    
@interface NSFileHandle (NSFileHandlePlatformSpecific)

#if defined(__WIN32__)
- (id)initWithNativeHandle:(void *)nativeHandle closeOnDealloc:(BOOL)closeopt;
- (id)initWithNativeHandle:(void *)nativeHandle;
- (void *)nativeHandle;
#endif	/* __WIN32__ */

