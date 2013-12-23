---
layout: page
title: HowToGetDeviceIDForGivenPath
---



How can I get the device ID (i.e. /dev/diskX) for a given path? I think you have to use the stat() function, but not 100% sure. Currently I'm just parsing the output of "/sbin/mount" which is ugly.

----

NSFileManager's instance method     fileSystemAttributesAtPath: might help.     NSFileSystemNumber is the keyed attribute that gives you the  file system number for the mounted file system that contains the path.  

----

That method works, but since I'm trying to find the device ID of a volume, and the volume is mounted on /, NSFileSystemNumber always returns 0 (or whatever is /). But I figured out how to do it with straight C calls. You have to use getmntinfo() which gives you an array of all mounted file systems (/sbin/mount uses method I believe exactly). You're also supposed to be able to use statfs() and pass it a file path, but I kept getting -1 from that function, not sure why. So I just created a function that loops through what getmntinfo() returns, and then created another function which parses the device ID (disk ID?) number from that:
    
#include <Cocoa/Cocoa.h>
#include <sys/param.h>
#include <sys/ucred.h>
#include <sys/mount.h>

// see "man statfs" and "man getmntinfo"

NSString *mountedFileSystemForVolumePath(NSString *volume)
{
	struct statfs *buf, *buf2;
	int i, count;
	const char *vol = [volume UTF8String];
	
	count = getmntinfo(&buf, 0);
	for (i=0; i<count; i++)
	{
		if (strcmp(buf[i].f_mntonname, vol) == 0)
			return [NSString stringWithUTF8String:buf[i].f_mntfromname];
	}
	return nil;
}

int deviceIDForVolumePath(NSString *volumePath)
{
	NSString *mfs = mountedFileSystemForVolumePath(volumePath);
	if (mfs && [mfs hasPrefix:@"/dev/disk"] && [mfs length] > 9)
	{
		return mfs substringWithRange:[[NSMakeRange(9, 1)] intValue];
	}
		
	return -1;	
}

int main(int argc, char *argv[])
{
	if (argc == 2)
	{
		NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

		NSString *volume = [NSString stringWithUTF8String:argv[1]];
		int deviceID = deviceIDForVolumePath(volume);
		NSLog(@"deviceID: %d", deviceID);

		[pool release];
	}
	
	return 0;
}

