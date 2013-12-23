---
layout: page
title: DiscMountedOrUnmountedNotification
---

Is there a way to receive a notification when a disk (specifically an ipod) is mounted or unmounted?

----

How about NSWorkspaceDidMountNotification

or

NSWorkspaceDidUnmountNotification?

There's also a willUnmount notification. Don't know if these work with iPods. They are described in the documentation for NSWorkspace.

----

Is there a way of detecting mounting of an Audio CD and playing it automatically?

I have a general idea of how to do it with an interface, but I'd like to make it a background app with no dock icon or window. Basically like System 7 thru OS 9 handled Audio CDs - once inserted, they played without any extra apps open.

----

Someone else may have a better idea, but you could try listening to the NSWorkspace NSWorkspaceDidMountNotification. I don't know how you'd determine whether the mounted device is an audio CD.

----

There is a setting in the CD/DVD system settings concerning the action to be taken when an Audio CD is inserted. The finder may send your app (once specified) an event to indicate you should play it. Perhaps a simple open AE with the volume path? I have not looked into it, so I may be way off. I would look at this first before writing code that assumes that your app is the designated player though.

*I did some tests -- no AppleEvents and no Finder. It seems likely that the CD/DVD settings thingy simply opens the application, which is then responsible to find out why it's been called (i.e. scan for Audio CDs and video DVDs).* -- EmanueleVulcano aka l0ne

----

**Check your notification center if things seem not to be working**

I am trying to use the NSWorkspaceDidMountNotification

and

NSWorkspaceDidUnmountNotification notifications to detect if a a drive has mounted so I can see if it's an iPod (the actual iPod detection code does work). Unfortunately the selector method is never called and I've tried mounting my iPod and a CD. The code I have atm is:

    
[nc addObserver:self
	selector:@selector(detectediPodNotification:)
	name:NSWorkspaceDidMountNotification
	object:nil];


I also have another notification that does work. Any help would be much appreciated :)

----

what is     nc? you need to get NSWorkspace's notification center, not the default center. From the docs:

*
All NSWorkspace notifications are posted to NSWorkspace�s own notification center, not the application�s default notification center. Access this center using NSWorkspace�s notificationCenter method.
*

Do you have detectediPodNotification: proprely defined? Is this properly defined in your class?

    
- (void)detectediPodNotification:(NSNotification *)note {

}


----

You may also wish to read MountedNotificationSentOnAwakeFromSleep

----

The following code will work for getting notifications about mounts.

    
- (void)awakeFromNib
	[[[NSWorkspace sharedWorkspace] notificationCenter] addObserver:self selector:@selector( didMount: )
		name:NSWorkspaceDidMountNotification object:nil];
	[[[NSWorkspace sharedWorkspace] notificationCenter] addObserver:self selector:@selector( didUnmount: )
		name:NSWorkspaceDidUnmountNotification object:nil];
}

- (void)didMount:(NSNotification *)notif
{
	NSLog( @"Hey someone mounted something at %@", notif userInfo] objectForKey:@"[[NSDevicePath"] );
}

- (void)didUnmount:(NSNotification *)notif
{
	NSLog( @"Hey someone unmounted something at %@", notif userInfo] objectForKey:@"[[NSDevicePath"] );
}


Unfortunately the behavior will differ due to iTunes. If iTunes isn't running and you insert a CD that (hasn't recently or ever?) been inserted you will get a notification of a mount on "/Volumes/Audio CD". But of course the default CDs & DVDs System Preference will cause iTunes to launch. iTunes will lookup the CD info, unmount the CD and remount it as the name of the album. Thereafter if you mount that CD you will still get the album name. There does appear to be a way to find out the file system ID and determine if it is an audio cd.

A more interesting question to me at the moment is if there is a programmatic way to change that system preference that launches iTunes on audio cd insertion?

----
Don't. The user has iTunes set to open **on purpose**. The quickest way to send your app to the Trash is to mess with the user's personal settings. -JonathanGrynspan
----
----
I think it is likely more accurate to say the user hasn't changed the default. I certainly wouldn't change it arbitrarily but I think a user might be equally annoyed with you telling them to go change a system preference so that your application can perform it's advertised functionality without being scooped by iTunes.
----
----
com.apple.digihub has a key called com.apple.digihub.cd.music.appeared but the value is numeric, and I'm not sure what it means

And if you class dump SystemUIServer you'll find classes dedicated to "DigiHub" (here's the dump)

**EDIT**: I've figured out how to change the prefs, check the DigiHub page for instructions (just don't change this preference without asking your user, if you do there's nothing wrong with changing it)

-FjolnirAsgeirsson
----

I'm sure there's a way through IOKit to find all the mounted CDs...

----

Not sure how old this is since these pages are not dated, but to help anyone else who might be looking at this page. You can do so using the DiskArbitration Framework. What follows is pretty much a boilerplate setup. You'll notice that the class is set up as a Singleton class, as that works better with the static callback functions. All initialization is still done in the -init function, and this singleton class will also function as expected if put in a NIB file.


Interface file:
    

#import <Cocoa/Cocoa.h>
#import <DiskArbitration/DiskArbitration.h>

@interface IKMediaController : NSObject {
	DASessionRef		_session;
	DADiskRef		_diskRef;
}

+(IKMediaController*)sharedInstance;

@end



Implementation File:
    

#import "IKMediaController.h"
#import <DiskArbitration/DiskArbitration.h>

@interface IKMediaController (Private)
-(void)mountVolume:(NSString*)deviceName;
-(void)unmountVolume:(NSString*)deviceName;
@end

#pragma mark DiskArbitration callback functions

static void diskAppearedCallback(DADiskRef disk, void * context);
static void diskDisppearedCallback(DADiskRef disk, void * context);
static void unmountCallback(DADiskRef disk, DADissenterRef dissenter, void * context);
static void ejectCallback(DADiskRef disk, DADissenterRef dissenter, void * context);

static void diskAppearedCallback(DADiskRef disk, void * context)
{
	[[IKMediaController sharedInstance] mountVolume:[NSString stringWithCString:DADiskGetBSDName(disk) encoding:NSASCIIStringEncoding]];
}

static void diskDisappearedCallback(DADiskRef disk, void * context)
{
	[[IKMediaController sharedInstance] unmountVolume:[NSString stringWithCString:DADiskGetBSDName(disk) encoding:NSASCIIStringEncoding]];
}

static void unmountCallback(DADiskRef disk, DADissenterRef dissenter, void * context)
{
	@try {
		if(NULL != dissenter) {
			DAReturn status = DADissenterGetStatus(dissenter);
			if(unix_err(status)) {
				int code = err_get_code(status);
				@throw [NSException exceptionWithName:@"IOException"
											   reason:@"Unable to unmount the disc." 
											 userInfo:[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:[NSNumber numberWithInt:code], [NSString stringWithCString:strerror(code) encoding:NSASCIIStringEncoding], nil] forKeys:[NSArray arrayWithObjects:@"errorCode", @"errorString", nil]]];
			}
			else {
				@throw [NSException exceptionWithName:@"IOException"
											   reason:@"Unable to unmount the disc." 
											 userInfo:[NSDictionary dictionaryWithObject:[NSNumber numberWithInt:status] forKey:@"errorCode"]];
			}
		}
		
		// Only try to eject the disc if it was already successfully unmounted
		DADiskEject(disk, kDADiskEjectOptionDefault, ejectCallback, context);
	}
	
	@catch(NSException *exception) {
		NSAlert *alert = [[[NSAlert alloc] init] autorelease];
		[alert addButtonWithTitle:(@"OK", @"General", @"")];
		[alert setMessageText:[NSString stringWithFormat:@"An error occurred on device %s.", DADiskGetBSDName(disk)]];
		[alert setInformativeText:[exception reason]];
		[alert setAlertStyle:NSWarningAlertStyle];		
		[alert runModal];
	}
}

static void ejectCallback(DADiskRef disk, DADissenterRef dissenter, void * context)
{
	@try {
		if(NULL != dissenter) {
			DAReturn status = DADissenterGetStatus(dissenter);
			if(unix_err(status)) {
				int code = err_get_code(status);
				@throw [NSException exceptionWithName:@"IOException"
											   reason:@"Unable to eject the disc." 
											 userInfo:[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:[NSNumber numberWithInt:code], [NSString stringWithCString:strerror(code) encoding:NSASCIIStringEncoding], nil] forKeys:[NSArray arrayWithObjects:@"errorCode", @"errorString", nil]]];
			}
			else {
				@throw [NSException exceptionWithName:@"IOException"
											   reason:@"Unable to eject the disc." 
											 userInfo:[NSDictionary dictionaryWithObject:[NSNumber numberWithInt:status] forKey:@"errorCode"]];
			}
		}
	}
	
	@catch(NSException *exception) {
		NSAlert *alert = [[[NSAlert alloc] init] autorelease];
		[alert addButtonWithTitle:@"OK"];
		[alert setMessageText:[NSString stringWithFormat:@"An error occurred on device %s.", DADiskGetBSDName(disk)]];
		[alert setInformativeText:[exception reason]];
		[alert setAlertStyle:NSWarningAlertStyle];		
		[alert runModal];
	}
}



@implementation IKMediaController

/*****************************************************\
 Singleton init methods ****************************|
\*****************************************************/

static IKMediaController *_sharedInstance = nil;

+(IKMediaController*)sharedInstance 
{
	@synchronized(self) {
		if(_sharedInstance == nil) {
			self alloc] init];
		}
	}
	return _sharedInstance;
}

-(id)init 
{
	Class thisClass = [self class];
	@synchronized(thisClass) {
		if(_sharedInstance == nil) {
			if(self = [super init]) {
				_sharedInstance = self;
				
				[[NSDictionary *match = [NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"IOCDMedia", [NSNumber numberWithBool:YES], nil] 
																  forKeys:[NSArray arrayWithObjects:(NSString *)kDADiskDescriptionMediaKindKey, kDADiskDescriptionMediaWholeKey, nil]];
				
				_session = DASessionCreate(kCFAllocatorDefault);
				DASessionScheduleWithRunLoop(_session, CFRunLoopGetCurrent(), kCFRunLoopDefaultMode);
				DARegisterDiskAppearedCallback(_session, (CFDictionaryRef)match, diskAppearedCallback, NULL);
				DARegisterDiskDisappearedCallback(_session, (CFDictionaryRef)match, diskDisappearedCallback, NULL);	
			}
		}
	}
	return _sharedInstance;
}

+(id)allocWithZone:(NSZone*)zone 
{
	@synchronized(self) {
		if(_sharedInstance == nil) {
			return [super allocWithZone:zone];
		}
	}
	return _sharedInstance;
}

- (id)copyWithZone:(NSZone *)zone { return self; }

- (id)retain { return self; }

- (unsigned)retainCount { return UINT_MAX; }

- (void)release {}

- (id)autorelease { return self; }

-(void)dealloc
{
	DAUnregisterCallback(_session, diskAppearedCallback, NULL);
	DAUnregisterCallback(_session, diskDisappearedCallback, NULL);
	DASessionUnscheduleFromRunLoop(_session, CFRunLoopGetCurrent(), kCFRunLoopDefaultMode);
	CFRelease(_session);
	
	[super dealloc];
}



-(void)mountVolume:(NSString*)deviceName
{
	[NSThread detachNewThreadSelector:@selector(initMountedDisc) toTarget:self withObject:nil];
}

-(void)initMountedDisc
{
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

       /* Right here goes the code for when your disc is mounted */

	[pool drain];
}

-(void)unmountVolume:(NSString*)deviceName
{
       /* Here goes the code to perform any cleanup when the disc is ejected */
}




-bryscomat

----
Is there a way to use the code posted above to get callbacks for USB mass storage devices, and things like iPhones?

