---
layout: page
title: LOiPod
---



(The title should be L0iPod with a zero.)

Requires linking to Carbon, too (for FSRef).

Instances of this class represent iPods that are connected and mounted (i.e. not ejected) to this computer. The class also provides a way to: (a) Get all currently connected iPods as instances of this class via     +allMountedDevices; (b) Know what 'family' of iPod we're dealing with (by using the same method that Apple uses with its iPod Software Updater, looking at the buildID/visibleBuildID fields of the hidden iPod system information file); and (c) Know if the iPod has some feature that you might be looking for (for example, if you want to export notes to the iPod, you can check whether the iPod has the Notes submenu with the     -hasNotes method).

Instances of this class "track" the iPod even across name changes (that is, if an instance is created and then the user changes the iPod's name in the Finder or in iTunes, the instance will change the result of its     -fileURL and     -path methods accordingly).

**Note: updated to correctly detect iPod with video playback (5th generation).**

Note: If you want to detect when an iPod is connected to your computer, observe NSWorkspace's notification, and test newly mounted removable devices with the     -initWithPath: method. If it returns a non-nil value, the new device is an iPod.

----
Example of use:

    
- (void) exportNotesToiPod {
    NSArray* alliPods = [L0iPod allMountedDevices];
    NSEnumerator* enu = [alliPods objectEnumerator];
    L0iPod device;

    while (device = [enu nextObject]) {
        if (![device hasNotes]) {
            NSRunAlertPanel([NSString stringWithFormat:@"The iPod named '%@' does not support notes. Only newer iPod models with a screen can display exported notes.", [device displayName]], nil, nil, nil, nil);
        } else {
            NSString* iPodPath = [device path];
            if (!iPodPath) continue; // iPod was unmounted before we could use it.
            NSString* notesPath = [iPodPath stringByAppendingPathComponent:@"Notes"];
            /* Do note exporting here */
        }
    }
}


L0iPod.h:

    

//  L0iPod.h

#import <Cocoa/Cocoa.h>
#import <Carbon/Carbon.h>

typedef enum {
    // internal, unused
    kL0iPodUnchecked = -1,
    
    // cannot determine the iPod type.
    kL0iPodGeneric = 0,
    
    // iPod, first or second generation
    kL0iPodMechanicalOrTouchWheel = 1,
    
    // iPod, third generation
    kL0iPodTouchWheelAndButtons = 2,
    
    // iPod mini (either first or second gen.)
    kL0iPodMini = 3,
    
    // iPod, fourth generation
    kL0iPodClickWheel = 4,
    
    // iPod photo or iPod with color display
    kL0iPodColorDisplay = 5,
    
    // iPod, fifth generation
    kL0iPodVideo = 6,
    
    // iPod nano
    kL0iPodNano = 7,
    
    // iPod shuffle
    kL0iPodShuffle = 128
} L0iPodFamily;

@interface L0iPod : NSObject {
    FSRef iPodRef;
    L0iPodFamily family;
}

// If the given path is inside an iPod, returns the absolute
// path to that iPod's mount point. Otherwise, returns nil.
+ (NSString*) deviceRootForPath:(NSString*) path;

// Is the given path an iPod mount point?
+ (BOOL) hasControlFolder:(NSString*) path;

// Returns the mount points of all mounted iPods.
+ (NSArray*) allMountedDevices;

// Initializes a L0iPod object that refers to the iPod that
// contains the given path. Returns the new object or nil if
// the path is not inside an iPod.
- (id) initWithPath:(NSString*) path;

// The file:// URL to the iPod mount point.
// Always returns the right URL, even if it has changed since
// you created this object. Returns nil if iPod was unmounted.
- (NSURL*) fileURL;

// The absolute path to the iPod mount point.
// Always returns the right path, even if it has changed since
// you created this object. Returns nil if iPod was unmounted.
- (NSString*) path;

// Returns the iPod's icon at a size of 32x32, as currently
// displayed by the Finder.
- (NSImage*) icon;

// Returns a dictionary containing the keys and values of the
// iPod's SysInfo file.
- (NSDictionary*) deviceInformation;

// Returns the iPod's family (one of the L0iPodFamily constants
// above).
- (L0iPodFamily) family;

// Does this iPod have a display?
- (BOOL) hasDisplay;

// Does this iPod have a color display?
- (BOOL) hasColorDisplay;

// Does this iPod have the Extras > Notes submenu?
- (BOOL) hasNotes;

// Can this iPod be connected to a TV (for photo or video
// viewing)?
- (BOOL) hasTVOut;

// Can this iPod play back video?
- (BOOL) hasVideoPlayback;

// Does this iPod have photo display capabilities?
- (BOOL) hasPhotoAlbum;

// The iPod's display name, as shown by the Finder, or
// nil if the iPod was unmounted.
- (NSString*) displayName;

@end



----

L0iPod.m:

    
//
//  L0iPod.m

#import "L0iPod.h"


@implementation L0iPod

+ (NSString*) deviceRootForPath:(NSString*) path {
    NSArray* arr = [[NSWorkspace sharedWorkspace] mountedRemovableMedia];
    NSEnumerator* enu = [arr objectEnumerator];
    NSString* root;
    
    while (root = [enu nextObject]) {
        if ([path hasPrefix:root] && [self hasControlFolder:root])
            return root;
    }
    
    return nil;
}

+ (BOOL) hasControlFolder:(NSString*) path {
    BOOL isDir;
    return [[NSFileManager defaultManager] fileExistsAtPath:[path stringByAppendingPathComponent:@"iPod_Control"] isDirectory:&isDir] && isDir;
}

+ (NSArray*) allMountedDevices {
    NSArray* arr = [[NSWorkspace sharedWorkspace] mountedRemovableMedia];
    NSEnumerator* enu = [arr objectEnumerator];
    NSMutableArray* ipods = [NSMutableArray arrayWithCapacity:[arr count]];
    
    NSString* path;
    while (path = [enu nextObject]) {
        if ([self hasControlFolder:path])
            [ipods addObject:[self alloc] initWithPath:path] autorelease;
    }
    
    return [NSArray arrayWithArray:ipods];
}

- (id) initWithPath:(NSString*) path {
    if (self = [super init]) {
        NSString* ipodRoot = self class] deviceRootForPath:path];
        if (!ipodRoot) {
            [self release];
            return nil;
        }
        
        [[CFURLGetFSRef((CFURLRef)[NSURL fileURLWithPath:ipodRoot], &iPodRef);
        
        family = kL0iPodUnchecked;
    }
    
    return self;
}

- (NSURL*) fileURL {
    NSURL* url = (NSURL*) CFURLCreateFromFSRef(NULL, &iPodRef);
    return [url autorelease];
}

- (NSString*) path {
    return self fileURL] path];
}

- ([[NSImage*) icon {
    NSString* path = [self path];
    return path == nil? nil : [[NSWorkspace sharedWorkspace] iconForFile:path];    
}

- (NSDictionary*) deviceInformation {
    NSString* ipod = [self path];
    if (!ipod)
        return nil;
    
    NSMutableDictionary* dict = [NSMutableDictionary dictionary];
    NSString* sysInfo = [NSString stringWithContentsOfFile:[ipod stringByAppendingPathComponent:@"iPod_Control/Device/SysInfo"]];
    NSScanner* scanner = [NSScanner scannerWithString:sysInfo];
        
    [scanner setCharactersToBeSkipped:[NSCharacterSet whitespaceCharacterSet]];
    
    while (![scanner isAtEnd]) {
        NSString* key = nil, * value = nil;
        [scanner scanUpToCharactersFromSet:[NSCharacterSet characterSetWithCharactersInString:@":\n"] intoString:&key];
        
        if ([scanner scanString:@":" intoString:nil]) {
            [scanner scanUpToString:@"\n" intoString:&value];
            [dict setObject:value forKey:key];
        }
        
        [scanner scanString:@"\n" intoString:nil];
    }
    
    return [NSDictionary dictionaryWithDictionary:dict];
}

- (L0iPodFamily)family
{
	if (family != kL0iPodUnchecked)
        return family;
	
	NSDictionary* info = [self deviceInformation];
	if (info == nil || [info count] == 0) {
		// it's most likely a shuffle...
		family = kL0iPodShuffle;
	} else {
		NSString *boardHwSwInterfaceRev = [info objectForKey:@"boardHwSwInterfaceRev"];
		if ([boardHwSwInterfaceRev hasPrefix:@"0x00010000"] || [boardHwSwInterfaceRev hasPrefix:@"0x00020000"]) {
			// mechanical/touch wheel (first & second generation)
			family = kL0iPodMechanicalOrTouchWheel;
		} else if ([boardHwSwInterfaceRev hasPrefix:@"0x00030001"]) {
			// touch wheel & buttons (third generation)
			family = kL0iPodTouchWheelAndButtons;
		} else if ([boardHwSwInterfaceRev hasPrefix:@"0x00040013"] || [boardHwSwInterfaceRev hasPrefix:@"0x00070002"]) {
			// iPod mini (1st and 2nd generations)
			family = kL0iPodMini;
		} else if ([boardHwSwInterfaceRev hasPrefix:@"0x00050013"] || [boardHwSwInterfaceRev hasPrefix:@"0x00050014"]) {
			// click wheel (fourth generation)
			family = kL0iPodClickWheel;
		} else if ([boardHwSwInterfaceRev hasPrefix:@"0x00060000"] || [boardHwSwInterfaceRev hasPrefix:@"0x00060004"]) {
			// iPod with color display (includes iPod photo)
			family = kL0iPodColorDisplay;
		} else if ([boardHwSwInterfaceRev hasPrefix:@"0x000C0005"]) {
			// iPod nano
			family = kL0iPodNano;
		} else if ([boardHwSwInterfaceRev hasPrefix:@"0x000B0005"]) {
			// iPod (with video playback)
			family = kL0iPodVideo;
		} else {
			// Unrecognized iPod
			family = kL0iPodGeneric;
		}
	}
	
	return family;
}

- (BOOL) hasDisplay {
    return [self family] != kL0iPodShuffle;
}

- (BOOL) hasColorDisplay {
    L0iPodFamily fam = [self family];
    return fam == kL0iPodColorDisplay || fam == kL0iPodNano || fam == kL0iPodVideo;
}

- (BOOL) hasNotes {
    L0iPodFamily fam = [self family];
    return (fam != kL0iPodMechanicalOrTouchWheel && fam != kL0iPodShuffle) || [[NSFileManager defaultManager] fileExistsAtPath:self path] stringByAppendingPathComponent:@"Notes"; //My nano fails the first test, so this way we can get a quick yes or do a further actual check for the folder
}

- (BOOL) hasTVOut {
    L0iPodFamily fam = [self family];
    return fam == kL0iPodColorDisplay || fam == kL0iPodVideo;
}

- (BOOL) hasVideoPlayback {
    return [self family] == kL0iPodVideo;
}

- (BOOL) hasPhotoAlbum {
    return [self hasColorDisplay];
}

- (NSString*) displayName {
    NSString* path = [self path];
    return path? [[NSFileManager defaultManager] displayNameAtPath:path] : nil;
}

@end



----

----
What license is this code under, if I want to include it in my app?

----
Consider this code public domain. -- EmanueleVulcano, the author.

----
Excellent! Thank you very much.

----
Emanuele, you may want to redo your iPod identification code by checking the "boardHwSwInterfaceRev" value. The current way should not be used and does not work correctly (buildID and visibleID I believe are used for the iPod Updater to determine which version of the OS the iPod is running). See the bottom of http://ipodlinux.org/Generations --KevinWojniak

----
It would be really nice to also detect an iPod mini color. Actually I think boardHwSwInterfaceRev is not very reliable, it looks like it is a combination of iPod hardware AND software versions. I've got a 1G iPod with boardHwSwInterfaceRev being 0x00010001 - that's maybe because it was shipped with updated software.
Apple uses buildID, VisibleBuildID, iPodFamily (and also updaterFamily, defaultColor) to detect the iPod generation and even a color of iPod mini, but to me it looks like  a mess of meaningless values.

----

If buildID and visibleID aren't good methods of detecting the iPod family (and I've heard conflicting info about boardHwSwInterfaceRev, which doesn't help either and it's the reason the two begincodes/endcodes above aren't merged yet), the only things left are the Apple catalog number "Mxxxx/A" and the serial number (for iPod mini colors -- but they're not very useful on the programming side of things, are they? and if you just want to display the right icon, you can simply call [ipod icon] to get the current icon for it).

Mmmh. Need definitive info before changing the code. -- EmanueleVulcano aka l0ne
----
EmanueleVulcano, how did you find out correct values for buildID and visibleID?
For boardHwSwInterfaceRev I'd suggest to check for such prefix as 0x000100, 0x000500, etc leaving the last 2 digits. Then this code will be more reliable.

----
I found the values inside the iPod Updater's resources, specifically inside iPod Updater <date>.app/Contents/Resources/UpdaterVersions.plist. This seems to be the standard way the updater recognizes iPods, as the only other kind of data that could be found in UpdaterVersions.plist that relates to the devices (as opposed to icons, resources and binary data in the updater itself) are serial code portions that can be used to distinguish between different colors of iPod mini (including between first and second gen iPod mini -- but since they're practically identical, I left all of this out of the class as it has low or no programmatical rilevance, and if you want the icon you can use -icon to get NSWorkspace's current icon for the iPod, which should suffice). If it's stable enough for Apple, it should do the trick for me, I thought. -- EmanueleVulcano aka l0ne

----
Hey, does this code support newest iPods? Could someone update it or share new buildID and visibleID numbers with us? (I think it would be a good idea to keep the organized list of such numbers here, for example: LOiPodSpecs)

----
The new 2G nano and 5.5G iPods will not work with this code, because the file SysInfo doesn't exist on them. There is a way through standard SCSI INQUIRY but I'm not sure how to do that in Cocoa (info is on ipodlinux.org which is down now).

----
Could anyone confirm - do these new iPods have iPod_Control (or iPod_Control/Device) folder? So at least we could easily detect that the mounted device is an iPod.
----
Yes, they have iPod_Control, like every other iPod (I think that will be there for a while as it holds the album art data, iTunes db, etc). SysInfo isn't there. What we need is a way to do this: http://www.ipodlinux.org/Device_Information - any takers?

----

If I'm not mistaken, we could use the IOKit to ask the system about the USB and FireWire devices present, and check for strings, manufacturer codes or product codes that are unique to the iPod family -- EmanueleVulcano aka millenomi

an example of interface for your code (L0iPod) you can make available it?

