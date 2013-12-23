---
layout: page
title: DockPrefsPrivate
---



Just some of the private CoreDock functions, which are used by the Dock prefpane.
Framework: /System/Library/Frameworks/ApplicationServices.framework/Frameworks/HIServices.framework

    
typedef enum {
	kCoreDockOrientationTop = 1,
	kCoreDockOrientationBottom = 2,
	kCoreDockOrientationLeft = 3,
	kCoreDockOrientationRight = 4
} CoreDockOrientation;

typedef enum {
	kCoreDockPinningStart = 1,
	kCoreDockPinningMiddle = 2,
	kCoreDockPinningEnd = 3
} CoreDockPinning;

typedef enum {
	kCoreDockEffectGenie = 1,
	kCoreDockEffectScale = 2,
	kCoreDockEffectSuck = 3
} CoreDockEffect;

// Tile size ranges from 0.0 to 1.0.
extern float CoreDockGetTileSize(void);
extern void CoreDockSetTileSize(float tileSize);

extern void CoreDockGetOrientationAndPinning(CoreDockOrientation *outOrientation, CoreDockPinning *outPinning);
// If you only want to set one, use 0 for the other.
extern void CoreDockSetOrientationAndPinning(CoreDockOrientation orientation, CoreDockPinning pinning);

extern void CoreDockGetEffect(CoreDockEffect *outEffect);
extern void CoreDockSetEffect(CoreDockEffect effect);

extern Boolean CoreDockGetAutoHideEnabled(void);
extern void CoreDockSetAutoHideEnabled(Boolean flag);

extern Boolean CoreDockIsMagnificationEnabled(void);
extern void CoreDockSetMagnificationEnabled(Boolean flag);
// Magnification ranges from 0.0 to 1.0.
extern float CoreDockGetMagnificationSize(void);
extern void CoreDockSetMagnificationSize(float newSize);


To be notified of changes to the Dock (by Dock.app and SystemPreferences), register your class as observer to the distributed notification "com.apple.dock.prefchanged" (using NSDistributedNotificationCenter).



TonyArnold says: there is a more up-to-date version, plus some new calls at http://code.google.com/p/undocumented-goodness/source/browse/trunk/CoreDock/CoreDockPrivate.h

