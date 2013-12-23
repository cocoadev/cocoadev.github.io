---
layout: page
title: WorkingWithAudioCDs
---



Hello -


I am developing a media player application, and wish to implement Audio CD handling and playback. I have a method that will theoretically work, however I would like to ensure that this method is the most efficient, since I can't find particularly clear information on the subject.


Possible method:
1. Detect volume mount with General/NSWorkspace
2. Check for presence of invisible .TOC.plist file created by Mac OS X's CDDA filesystem handler
3. List the contents of the CD alphabetically, then play back each track by opening the illusionary .aiff files using Quicktime


Are there any better ways (for example, a system notification of Audio CD insertion)? Also, any other suggestions related to working with Audio General/CDs is welcomed (even to the extent of CDDB/General/FreeDB functionality).

Thanks!

----
CDDB access costs money unless you're releasing a freeware app; General/FreeDB unfortunately has shut down.

----

If your app is for 10.4 and up, you can use the General/DiskArbitration framework.  To do this, you need to create a General/DASession that you will use as your context for all calls to the framework.

To do this, and to register for mount and unmount notifications:

    
// Only request mount/unmount information for audio General/CDs
General/NSDictionary *match = General/[NSDictionary dictionaryWithObjects:General/[NSArray arrayWithObjects:@"General/IOCDMedia", General/[NSNumber numberWithBool:YES], nil] forKeys:General/[NSArray arrayWithObjects:(General/NSString *)kDADiskDescriptionMediaKindKey, kDADiskDescriptionMediaWholeKey, nil]];
		
_session = General/DASessionCreate(kCFAllocatorDefault);
General/DASessionScheduleWithRunLoop(_session, General/CFRunLoopGetCurrent(), kCFRunLoopDefaultMode);
General/DARegisterDiskAppearedCallback(_session, (General/CFDictionaryRef)match, diskAppearedCallback, NULL);
General/DARegisterDiskDisappearedCallback(_session, (General/CFDictionaryRef)match, diskDisappearedCallback, NULL);


Then, when a disc is mounted (or your app is first launched), diskApperaredCallback will be called.

In my code I have a singleton object that handles the mount/unmount notifications, so I don't use the context above.  It would be normal practice to pass self.  When your callback is invoked, you can use General/DADiskGetBSDName() to get the device name, and then use the DKIOCD* ioctls for accessing the disc's TOC and audio data (and subchannel data if desired).  For example, the following code is the kind of thing you could do to open a CD and read the TOC.  I omitted any error checking for brevity.

    
const char *bsdName = General/DADiskGetBSDName(disk); // disk comes from the diskAppearedCallback
int fd = opendev(bsdName, O_RDONLY | O_NONBLOCK, 0, NULL);

dk_cd_read_toc_t cd_read_toc;
uint8_t buffer [2048];
	
bzero(&cd_read_toc, sizeof(cd_read_toc));
bzero(buffer, sizeof(buffer));
	
cd_read_toc.format = kCDTOCFormatTOC;
cd_read_toc.buffer = buffer;
cd_read_toc.bufferLength = sizeof(buffer);
	
int result = ioctl(fd, DKIOCCDREADTOC, &cd_read_toc);

// Read the descriptors
close(fd);


Finally, when you're finished, clean up:

    
General/DAUnregisterCallback(_session, diskAppearedCallback, NULL);
General/DAUnregisterCallback(_session, diskDisappearedCallback, NULL);
General/DASessionUnscheduleFromRunLoop(_session, General/CFRunLoopGetCurrent(), kCFRunLoopDefaultMode);
General/CFRelease(_session);


-sbooth
