---
layout: page
title: NSSyncConduit
---



Note: This data applies to the latest gleaned info from production OS X - 10.3.5 as of this writing.

iSync uses a closed API defined in /System/Library/PrivateFrameworks/SyncConduit.framework to define it's conduits (pieces of software that provide sync servicing for individual devices).  As this is a Private Framework, Headers are not available and were gleaned from ClassDump.  The tricky part of course is translating all the fun (id) defs in ClassDump -ed headers to real objects... and determining the whys and hows about these individual objects going in and out.

I (BrendanWMcAdams) have been doing some work on puzzling out NSSyncConduit myself over at www.twodot.org.  My coding partner and I thought it might be nice to kick back some of the info we've puzzled to CocoaDev, so here goes...

This is the results of my work, off of the 10.3.5 verion of SyncConduit...

    
/*
 * Decoded from /System/Library/PrivateFrameworks/SyncConduit.framework
 * 
 * basic structure from class-dump
 * 
 * Actual datastructures and functionality details decoded by 
 * Brendan W. McAdams <bmcadams@twodot.org>    
 *
 */

#import <Foundation/Foundation.h>

@interface NSSyncConduit : NSObject
{
}

/** 
 * This is a simple name describing the conduit, 
 * e.g. "ExchangeConduit" 
*/
+ (NSString*) conduitName;

/** 
 * Not sure what this is supposed to indicate...
 * May be related to whether there's a full app 
 * doing the work, with a gui, or if iSync should
 * handle display, UI, etc.
 */
+ (BOOL) isApplication;

/** 
 * This returns an NSArray, 
 * which contains NSString objects.
 * Each NSString indicates a class name 
 * which is an NSSyncDataClass.
 * [having trouble dumping the stuff for this].
 * 
 * The DataClasses returned are the forms of data
 * that this conduit will support syncing.
 * 
 * DataClasses represent types of syncing.
 * The only DataClasses I know about are:
 *     - NSSyncContactDataClass (AddressBook)
 *     - NSSyncICalendarDataClass (iCal)
 *  
 * I haven't dug deep but there are likely bookmark ones, etc.
 * for .mac and Safari
 */
+ (NSArray*) supportedDataClasses;

/** 
 * This returns an NSArray,
 * which contains NSConduit objects,
 * which are presumably instances of the 
 * local conduit's own subclass.
 * This is obviously most useful 
 * In the case where the conduit allows
 * you to configure multiple sync devices 
 * i.e. iMac account, iPod and phone.
 */
+ (NSArray*) activeConduits;

/**
 * This returns an NSString 
 * containing an ID for the particular 
 * instance of device (possibly, e.g. IMEI)
 * this instance is syncing.
 * Keep in mind an instance refers to a 
 * particular device, rather than type of device
 */
- (NSString*) deviceId;

/**
 * This returns an NSString 
 * containing a name or the particular 
 * instance of device (possibly, e.g. bluetooth 'short name')
 * this instance is syncing.
 * Keep in mind an instance refers to a 
 * particular device, rather than type of device
 */
- (NSString*) deviceName;

/**
 * This returns an NSString 
 * containing the fully qualified path to 
 * an icon representing the device (.tiff or .icns seem used)
 * This icon is used in the 'add device' search as well as
 * the little bar that shows devices you have setup to sync.
 * This is an instance method as it's possible for a conduit, 
 * (e.g. symbianconduit) to handle multiple 'similar devices'.
 * A great example is that my instance of symbian conduit is 
 * for my Nokia 3650, but could also do an N-Gage, etc.
 * The symbian conduit has icons for multiple devices and shows 
 * them appropriate.
 */
- (NSString*) iconPath;


/** 
 * This returns an NSArray, 
 * which contains NSString objects.
 * Each NSString indicates a class name 
 * which is an NSSyncDataClass.
 * [having trouble dumping the stuff for this].
 *  
 * The DataClasses returned are the forms of data
 * that this conduit will support syncing.
 * 
 * DataClasses represent types of syncing.
 * The only DataClasses I know about are:
 *     - NSSyncContactDataClass (AddressBook)
 *     - NSSyncICalendarDataClass (iCal)
 *  
 * I haven't dug deep but there are likely bookmark ones, etc.
 * for .mac and Safari
 */
- (NSArray*) activeDataClasses;

- (int)openSync:(id)fp12;
- (id)synchronizerForDataClass:(id)fp12;
- (void)finishedSynchronizingDataClass:(id)fp12 synchronizer:(id)fp16;

/**
 * Not 100% sure on the content of the dictionary; 
 * our testing hasn't finished yet.
 * However, it's likely related to changes, 
 * differences and / or conflicts.
 */
- (NSDictionary*) closeSync;

@end



(some return and parameter types have been edited in a best guess manner... More data is forthcoming as we hack away)

----

-- This is the original dump from this page, I believe it's from an old version of OS .. 10.2? --
Here some first learnings about NSSyncConduit (iSync):

iSync uses plugin bundles (suffix .conduit) stored at /System/Library/SyncServices. These apparently define a subclass of NSSyncConduit which is part of the private framework SyncConduit (/System/Library/PrivateFrameworks)

ClassDump of a conduit reveals (crashes on SyncConduit itself):

    
@interface xxConduit : NSSyncConduit

+ (id)iconPathForDeviceModel:(id)fp12;
+ (NSString *)conduitName; - human readable name of the conduit bundle
+ (NSArray *)supportedDataClasses; - array of data classes (e.g. ABPerson etc.) that are supported
+ (NSArray *)activeConduits; - ???
+ (BOOL)isApplication; - ???

- (NSString *)deviceId; - device ID (unique)
- (NSString *)deviceName; - device name (e.g. Cellphone name; conduit might support more than one Cellphone)
- (NSString *)iconPath; - path to Icon to be shown in iSync view
- (BOOL)canGenerateChanges; - ???
- (NSArray *)activeDataClasses; - ???
- (int)openSync:(id)fp12; - ???
- (id)synchronizerForDataClass:(Class)fp12; - ??? does this return a synchronizer object which is a subclass of NSSyncDBSynchronizer
- (void)_secretMethodToRemoveDeviceForever; - ???
- (void)finishedSynchronizingDataClass:(id)fp12 synchronizer:(id)fp16; - ???
- (id)closeSync; - ???
- (BOOL)canFastSync;

@end



I don't know if this might help but someOne anonymous post a code on blackberry forum:
http://www.blackberry.com/developers/forum/post.jsp?forum=1&thread=516&message=9211&reply=true&quote=true

    
#import <Foundation/Foundation.h>

@interface NSSyncConduitManager : NSObject
{
}

+ (NSSyncConduitManager*)conduitManager;
- (NSArray*)conduitClasses;

@end

@interface TESTSyncConduit : NSObject
{
}

+ (NSString*)conduitName;
+ (BOOL)isApplication;
+ (NSArray*)supportedDataClasses;
+ (NSArray*)activeConduits;
+ (NSArray*)activeDataClasses;
- (id)synchronizerForDataClass:(NSString*)dataClass ;

@end

@interface NSSyncDBSynchronizer : NSObject
{
}

- (NSDictionary*)database;

@end


int main( int argc, char** argv )
{
NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];

NSSyncConduitManager* aConduitManager = [NSSyncConduitManager conduitManager];
if( aConduitManager != nil )
{
NSArray* conduitClasses = [aConduitManager conduitClasses];

int i = 0;
for( ; i < [conduitClasses count]; i++ )
{
TESTSyncConduit* curConduitClass = [conduitClasses objectAtIndex:i];

NSLog( @"CONDUIT NAMED %@", curConduitClass class] conduitName] );

[[NSArray* supportedDataClasses = curConduitClass class] supportedDataClasses];

[[NSArray* activeConduits = curConduitClass class] activeConduits];
if( activeConduits != nil && [activeConduits count] > 0 )
{
int j = 0;
for( ; j < [activeConduits count]; j++ )
{
[[TESTSyncConduit* aConduit =
[activeConduits objectAtIndex:j];
if( aConduit != nil && aConduit class]isApplication] == NO )
{
int k = 0;
for( ; k < [supportedDataClasses count]; k++ )
{
[[NSString* supportedClass = [supportedDataClasses objectAtIndex:k];
NSSyncDBSynchronizer* syncher = [aConduit synchronizerForDataClass:supportedClass];

NSLog( @"SYNCHRONIZER FOR DATA CLASS %@", supportedClass );
NSLog( @"NOW HERE'S WHERE IT GETS INTERESTING: DATABASE IS: %@", [[syncher database] description] );
}
}
}
}
}
}

[pool release];

return 0;
}


