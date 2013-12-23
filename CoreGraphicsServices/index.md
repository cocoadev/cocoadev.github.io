---
layout: page
title: CoreGraphicsServices
---

    
/*
    Call 			no# args	arg 			type
	
    CGSEndContext		1 argument	?			int				-- possibly unneeded by CGContextFlush
    CGSFlushContext		1 argument 	?			int				-- use CGContextFlush instead
    CGSImage			2 arguments	?, ?			int , int
    CGContextSetAlpha		1 argument 	?			int
    CGSScaleCTM			2 arguments	?, ?			float, float
    CGSTranslateCTM		2 arguments 	?, ?			float, float
    CGSSetGStateAttribute	3 arguments	?, ?, ?			int,int,int
    CGSUniqueCString		1 argument	?			int 		(constant 0x1fa8)
    CGSReadObjectFromCString	1 argument	?			int		(constant 0x2140)
    CGSClearContext		1 argument	?			int
    CGSGetWindowAlpha		3 argument	?,?,?			int,int,int
    CGSPutBooleanForCStringKey	3 arguments	?,?,?			int,int,int
    CGSPutIntegerForCStringKey	3 arguments	?,?,?			int,int,int
    CGSCreateDictionary		1 argument	?			int
    CGSReleaseWindow		2 arguments	?,?			int,int    -- deprecated? use CGContextRelease?
    CGSSetWindowWarp		5 arguments	?,?,?,?,?		int,int,int,int,int
    CGSSetBackgroundEventMaskAndShape 3 args	?,?,?			int,int,int
    CGSInputModifierKeyState	2 arguments	?,?			int,int 	(const arg1=0,arg2=variable)
    CGSGetScreenRectForWindow	1(2?) arguments ?,(?)			int,(int)
    CGSSetFillPattern		2 args		?,?			int,int
    CGSCurrentInputPointerPosition 1 args	?			int
    CGSSetWindowClipShape	3 arguments	?,?,?			int,int,int
    CGSGetScreenRectForWindow	2 arguments	?,?			int,int
    CGSGetWindowFlushSeed	3 arguments	?,?,?			int,int,int
    CGSGetCurrentCursorLocation 1 argument	?			int
*/

#ifndef _CGS_HACK_H
#define _CGS_HACK_H

#include <Carbon/Carbon.h> /* for ProcessSerialNumber */

typedef void *CGSConnectionID;
typedef void *CGSValueObj;
typedef void *CGSRegionObj;
typedef void *CGSBoundingShapeObj;

typedef enum _CGSWindowOrderingMode {
    kCGSOrderAbove                =  1,
    kCGSOrderBelow                = -1,
    kCGSOrderOut                  =  0
} CGSWindowOrderingMode;

#define kCGSNullConnectionID ((CGSConnectionID)0)

extern CGSConnectionID _CGSDefaultConnection(void);

extern void CGSReenableUpdate(CGSConnectionID cid);
extern void CGSDisableUpdate(CGSConnectionID cid);

extern OSStatus CGSSetWindowTransforms(const CGSConnectionID cid, CGWindowID *wid, CGAffineTransform *transform, int n);
extern OSStatus CGSSetWindowTransform(const CGSConnectionID cid, CGWindowID wid, CGAffineTransform transform);

extern OSStatus CGSGetWindowTransform(const CGSConnectionID, CGWindowID wid, CGAffineTransform *outTransform);
    
// questionable, partly guessed IIRC.
extern OSStatus CGSSetSharedWindowState(const CGSConnectionID cid, CGWindowID wid, CGSValueObj boolean);
extern OSStatus CGSSetWindowAlpha(const CGSConnectionID cid, CGWindowID wid, float alpha);
    
extern OSStatus CGSSetWindowProperty(const CGSConnectionID cid, CGWindowID wid, CGSValueObj key, CGSValueObj value);

extern CGSValueObj CGSCreateCString(char *string);
extern CGSValueObj CGSCreateBoolean(Boolean bool);
extern void CGSReleaseGenericObj(CGSValueObj obj);

extern OSStatus CGSOrderWindow(CGSConnectionID cid, CGWindowID wid, CGSWindowOrderingMode place, CGWindowID relativeToWindowID /* can be NULL */);

extern void CGSNewRegionWithRect(const CGRect *aRectangle, CGSRegionObj *outRegionObj);
extern OSStatus CGSReleaseRegion(CGSRegionObj);
extern void CGSGetRegionBounds(const CGSRegionObj aRegion, CGRect *outRect);

extern void CGSSetWindowOpacity(CGSConnectionID cid, CGWindowID wid, void* opacity /* kCGSFalse, is that a CGSValueObj or standard char? */);

extern OSStatus CGSNewConnection(void *something /* can be NULL, parent connection? */, CGSConnectionID *outID);
extern OSStatus CGSReleaseConnection(CGSConnectionID cid);
extern void CGSInitialize();

extern OSStatus CGSGetConnectionIDForPSN(const CGSConnectionID cid, ProcessSerialNumber *psn, CGSConnectionID *out);

// random hack constants for CGSSetDebugOptions
#define kCGSDebugOptionNormal 0
#define kCGSDebugOptionNoShadows 16384
#define kCGSHDumpWindowInfoToFile (0x8000<<16)|1

extern OSStatus CGSSetDebugOptions(unsigned long);

// only works if you kill the dock, then stops dock from relaunching
extern OSStatus CGSSetUniversalOwner(const CGSConnectionID cid, int);
// apparently a nop
extern OSStatus CGSSetOtherUniversalConnection(const CGSConnectionID cid);

extern OSStatus CGSGetScreenRectForWindow(CGSConnectionID cid, CGWindowID wid, CGRect *outRect);
extern OSStatus CGSMoveWindow(CGSConnectionID cid, CGWindowID wid, CGPoint *aPoint);

extern OSStatus CGSGetWindowLevel(CGSConnectionID cid, CGWindowID wid, CGWindowLevel *level);
extern OSStatus CGSGetWindowBounds(CGSConnectionID cid, CGWindowID wid, CGRect *bounds);

extern CGWindowID CGSDesktopWindow(void);

extern OSStatus CGSSetWindowLevel(CGSConnectionID cid, CGWindowID wid, CGWindowLevel level);

#endif /* _CGS_HACK_H */


----

I removed the CGSWindowID type, as it's actually the same as the CGWindowID type that's publicly defined. -JonathanGrynspan

