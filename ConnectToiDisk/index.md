---
layout: page
title: ConnectToiDisk
---

How do I connect to an iDisk? I just want a basic mount that logs in, and then opens the Sites folder.

----

It depends what you want to do really. At the most basic level you use open a URL using NSWorkspace, or you could run the webdav mount program.

----

Or use the .Mac framework, which is a public API. --FrederickCheung

See http://developer.apple.com/internet/dotmackit.html

----

DotMacKit is great, but it doesn't actually mount the iDisk.  If you want to mount the iDisk so that you can have an NSOpenPanel begin on the iDisk then you need to do something like this:

    
- (NSString *) iDiskPathForUser: (NSString *) username
{
	NSString *mountediDisk = nil;
	struct statfs *buf;
	int i, count;
	const char *idiskName = [[NSString stringWithFormat: @"http://idisk.mac.com/%@/", username] UTF8String];
	count = getmntinfo(&buf, 0);
	for(i=0; i < count; i++) {
		if(strcmp(buf[i].f_mntfromname, idiskName) == 0)
			mountediDisk = [NSString stringWithUTF8String: buf[i].f_mntonname];
	}
	return mountediDisk;
}
- (void) connectToiDisk: (NSMutableDictionary *) dictionary;
{
	// we're in a new thread so we need a new pool.
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

	// check to see if the iDisk is already mounted.
	NSString *mountediDisk = [self iDiskPathForUser:[dictionary valueForKey: @"username"]];
	
	// mount the iDisk only if its not already mounted.
	// we mount the iDisk synchronously because we are in our own thread. (I'm assuming this....)
	if(mountediDisk == nil) {
		FSVolumeOperation volumeOp;
		OSStatus err = FSCreateVolumeOperation(&volumeOp);
		if (err == noErr) {
			err = FSMountServerVolumeSync((CFURLRef)[dictionary valueForKey: @"url"], NULL, (CFStringRef)[dictionary valueForKey: @"username"], (CFStringRef)[dictionary valueForKey: @"password"], NULL, 1);
			if (err != noErr) {
				FSDisposeVolumeOperation(volumeOp);
			}
		}
	mountediDisk = [self iDiskPathForUser:[dictionary valueForKey: @"username"]];
	}
	[pool release];
}

 

--AdhamhFindlay

