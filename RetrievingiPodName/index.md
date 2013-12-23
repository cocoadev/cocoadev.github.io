---
layout: page
title: RetrievingiPodName
---



I thought I'd share some code on how to properly read an iPod's name if you're writing a program that interfaces with the iPod in any way. Most programs just use the iPod's filename as its name, but this can be different then the name set in iTunes, especially for Windows formatted iPods being read by a Mac. To get the name, you must read the D<nowiki/>eviceInfo file stored on the iPod. Enjoy! --KevinWojniak

    - (NSString *)getNameForiPodAtPath:(NSString *)iPodPath
{
	NSString *deviceInfoPath = [iPodPath stringByAppendingPathComponent:@"iPod_Control/iTunes/DeviceInfo"];
	NSData *data = [NSData dataWithContentsOfFile:deviceInfoPath];
	if (data == nil || [data length] == 0)
		return nil;

	const char *bytes = [data bytes];
	unsigned short iPodNameLength = [self readShort:bytes position:0x0];
	return [self getDeviceInfoStringFromBytes:bytes position:0x2 length:iPodNameLength];
}

- (NSString *)getDeviceInfoStringFromBytes:(const char *)bytes position:(unsigned int)position length:(unsigned int)length
{
	NSMutableString *str = [NSMutableString string];
	int i;
	for (i=0; i<length*2; i+=2)
	{
		short c = [self readShort:bytes position:i + position];
		[str appendFormat:@"%C", c];
	}
	return str;
}

- (unsigned short)readShort:(const char *)bytes position:(unsigned int)position
{
	unsigned short value = *(unsigned short*)&bytes[position];
	return CFSwapInt16LittleToHost(value);
}


*Modified to use a pointer to eliminate shifting and addition operations.  Also, length should be unsigned.*


------
Unfortunately this does not work reliably, as the newer ipods (in particular the nano) does not create this file.  I've looked and looked to no avail for where the nano stores the name of the ipod on the disk. Can anyone find it?

*Yes, the nanos do not create this file. However I believe the iTunesDB stores the name of the iPod as the name of the main iPod playlist.. so you could try parsing this file for that...* http://ipodlinux.org/ITunesDB

----

This is how I did it, but as said above it might not work with iPod nano, haven't tried 'em yet. Anyway its a simple alternative:

    
- (void)awakeFromNib
{
	// NOTIFIES SOFTWARE ABOUT NEW CONNECTED IPODS
	[[[NSWorkspace sharedWorkspace] notificationCenter] addObserver:self selector:@selector(newiPod:) name:NSWorkspaceDidMountNotification object:nil];
}


- (void)newiPod:(NSNotification *)notification
{
	NSString *newiPodPath = [NSString stringWithFormat:@"%@/iPod_Control/Device", notification userInfo] objectForKey:@"[[NSDevicePath"]];
       NSLog(@"%@", newiPodPath);
}


Of course then you have to check if the newiPodPath exists, and if it does you have yourself an iPod. In most cases ;-)
- FernandoLucasSantos

*That simply only detects when an iPod is connected. And yes, the easy alternative for getting an iPod's name when the DeviceInfo file doesn't exist is to just use the mounted file system name. But, this isn't the preferred method when working with most iPods since FAT32 formatted iPods show up in all caps in the file system. That's why I created this page in the first place :-)*

----

Also, another easy way to do it is to get the iPod path (something like /Volumes/myPod) and then use NSFileManager's displayNameAtPath to get the actual name that shows in the Finder. Often the iPod mounts as /Volumes/myPod 1 or something like that, but will be displayed as myPod, and displayNameAtPath fixes that. You can also parse the iTunesDB file on the iPod to get the name of the iPod for when you have a Windows (FAT) formatted iPod and the name in the Finder shows up all caps but in iTunes it has actual formatting.
----
While changing the iPod's name in iTunes will change the volume label, the converse is not true. Therefore     displayNameAtPath: will give you the name of the iPod *volume*, but not the name assigned to the iPod itself in iTunes.

----

Again, LOiPod might provide better mileage here. Although its -displayName uses NSFileManager, it can return the iPod's SysInfo file as a dictionary, which might contain the volume name (don't remember right now). -- EmanueleVulcano aka l0ne

----

No, the name is not in SysInfo. The name is either in DeviceInfo (which new iPods don't use anymore) or the iTunesDB.

