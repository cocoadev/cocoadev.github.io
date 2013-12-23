---
layout: page
title: DigiHub
---

Private framework that seems to handle disk/dvd/ipod insertion. It's preferences are stored in com.apple.digihub

It's preferences have a numeric value. When I change the default action from Opening iTunes to ignore, the value of [com.apple.digihub.cd.music.appeared key:action] changes to 1 (is 101 when you set it to open iTunes)

When I set the prefs to open a custom app it becomes:
    
    "com.apple.digihub.cd.music.appeared" = {
        action = 5; 
        otherapp = {
            "_CFURLString" = "Path to application"; 
            "_CFURLStringType" = 0; 
        }; 
    }; 

So, conclusion: to customize what happens when you insert a cd, open the defaults domain "com.apple.digihub". and set the preferences like above 

when class dumping SystemUIServer you'll find 2 classes related to DigiHub, here's the dump:

    
@interface DigiHub : NSObject
{
    struct __CFRunLoopSource *_diskArbSource;
    NSDictionary *_defaultActions;
    NSDictionary *_bundleIDMapper;
    NSMutableDictionary *_digiHubPrefs;
    NSMutableDictionary *_specificMedia;
    NSMutableArray *_specificMediaList;
    NSMutableDictionary *_specificiPods;
    NSMutableArray *_specificiPodList;
    NSMutableDictionary *_specificDVCameras;
    NSMutableArray *_specificDVCameraList;
    NSMutableDictionary *_askDialogList;
    NSMutableDictionary *_ejectList;
    NSString *_uiscriptrunnerPath;
    NSMutableDictionary *_ipods;
    NSDictionary *_mountControls;
    NSDictionary *_unmountControls;
    NSDictionary *_ejectControls;
    BOOL _isConsoleSession;
}

- (id)init;
- (void)dealloc;
- (void)setConsoleSession:(BOOL)fp8;
- (BOOL)consoleSession;

@end

@interface DigiHub (private)
- (void)_getDigiHubPrefs;
- (void)_m5PrefsChanged:(id)fp8;
- (void)_m5PrefsChanged:(id)fp8 sync:(BOOL)fp12;
- (void)_prefsChanged:(id)fp8;
- (void)_registerWithIOKit;
- (void)_delayedUSBDeviceFirstMatch:(id)fp8;
- (void)_delayedFireWireDeviceFirstMatch:(id)fp8;
- (void)_registerWithDiskArb;
- (void)_ipodAppeared:(id)fp8 afterLogin:(BOOL)fp12 oldPref:(struct
iPodTunesPref *)fp16 newPref:(struct iPodTunesPrefNew *)fp20;
- (BOOL)_diskAppeared:(char [1024])fp8 flags:(unsigned int)fp12
mountpoint:(char [1024])fp16 ioContent:(char [1024])fp20 afterLogin:
(BOOL)fp24;
- (void)_blankDiskAppeared:(char [1024])fp8 type:(int)fp12;
- (void)_diskEjected:(char [1024])fp8;
- (void)_ejectPostError:(char [1024])fp8 pid:(int)fp12;
- (int)_preMount:(char [1024])fp8 name:(char [1024])fp12 flags:
(unsigned int)fp16;
- (int)_preUnmount:(char [1024])fp8;
- (int)_preEject:(char [1024])fp8;
- (id)_getAskDialogList;
- (void)_removeAskDialog:(id)fp8;
- (id)_getEjectList;
- (BOOL)_isIdentifierInAskDialogList:(id)fp8;
- (void)askDialogDismissed:(id)fp8 withKey:(id)fp12;
- (BOOL)_doAction:(int)fp8 event:(id)fp12 key:(id)fp16;
- (BOOL)_handleEventInScript:(id)fp8 eventInfo:(id)fp12;
- (long)_createAppleEvent:(unsigned long)fp8 psn:(struct
CPSProcessSerNum *)fp12 info:(id)fp16 outAE:(struct AEDesc *)fp20;
- (BOOL)_handleEventInApp:(id)fp8 path:(id)fp12 bundleID:(id)fp16
eventInfo:(id)fp20;
- (void)_savePreference:(id)fp8;
- (id)_haveISeenThisiPod:(id)fp8;
- (void)_addiPod:(id)fp8;
- (void)_removeiPod:(id)fp8;
- (id)_findPodForDisk:(char [1024])fp8;
- (id)_iPods;
- (void)_handleSCSIPeripheralDeviceNubNotification:(unsigned int)fp8
newiPod:(BOOL)fp12;
- (BOOL)_canSupportVideoConference;
- (void)_iidcCameraAppeared:(BOOL)fp8;
- (void)_applicationWillTerminate:(id)fp8;
- (void)_launchICAppFromPref:(id)fp8 device:(struct _ICNDeviceRec *)
fp12 defaultID:(id)fp16;
- (void)_launchICAppForDigiCamera;
@end

@interface DigiHubEvent : NSObject
{
    NSString *_event;
    int _action;
    int _defaultAction;
    NSURL *_app;
    NSURL *_script;
    unsigned int _aeEventID;
    BOOL _performAtLogin;
}

+ (id)digiHubEvent:(id)fp8 defaultAction:(int)fp12 appleEventID:
(unsigned long)fp16 performAtLogin:(BOOL)fp20;
+ (id)digiHubEvent:(id)fp8 withDictionary:(id)fp12 defaultAction:(int)
fp16 appleEventID:(unsigned long)fp20;
- (id)initWithEvent:(id)fp8 withDictionary:(id)fp12 defaultAction:
(int)fp16 appleEventID:(unsigned long)fp20;
- (id)initWithEvent:(id)fp8 action:(int)fp12 app:(id)fp16 script:(id)
fp20 defaultAction:(int)fp24 appleEventID:(unsigned long)fp28
performAtLogin:(BOOL)fp32;
- (void)dealloc;
- (id)event;
- (unsigned long)appleEventID;
- (int)action;
- (BOOL)performAtLogin;
- (void)setAction:(int)fp8;
- (void)setAction:(int)fp8 script:(id)fp12;
- (void)setAction:(int)fp8 app:(id)fp12;
- (int)defaultAction;
- (void)setDefaultAction:(int)fp8;
- (id)app;
- (void)setApp:(id)fp8;
- (id)script;
- (void)setScript:(id)fp8;
- (id)createDictionary;

@end

