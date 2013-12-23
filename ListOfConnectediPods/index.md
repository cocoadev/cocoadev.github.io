---
layout: page
title: ListOfConnectediPods
---



Ever wanted to get a list of all connected iPods?
    
#include <sys/param.h>
#include <sys/ucred.h>
#include <sys/mount.h>

NSMutableArray *iPods = [NSMutableArray array];
NSFileManager *fm = [NSFileManager defaultManager];
struct statfs *buf;
int i, count;

count = getmntinfo(&buf, 0);
for (i=0; i<count; i++)
{
	if ((buf[i].f_flags & MNT_LOCAL) == MNT_LOCAL)
	{
		NSString *path = [NSString stringWithUTF8String:buf[i].f_mntonname];
		if ([fm fileExistsAtPath:[path stringByAppendingPathComponent:@"iPod_Control"]])
			[iPods addObject:path];
	}
}


----

Also,     buf[i].f_fstypename returns a c-string that is "hfs" for Mac iPods, and "msdos" for Windows iPods. More info at     man statfs.

--KevinWojniak

----

You might get better mileage out of LOiPod, I think. -- EmanueleVulcano aka l0ne

----

IMO what I've found over continual use of mountedRemovableMedia for accessing all iPods, is that it is not reliable, and using statfs definitely is.

----

I have a few criticisms :)
One: how is -mRM not reliable? Note that **polling** it isn't reliable, but I've never seen a volume being mounted without a NSWorkspace notification being sent correctly (which is the approach I tend to use with LOiPod: do initialization once with the -mRM wrapper and then listen to NSWorkspace's NSWorkspaceDidMountNotification to update my UI. I need to integrate this thing in LOiPod, I believe, but I don't have the time or motivation at the moment to work on it).

Second, a minor nitpick: using UTF8String for paths is OK but not reliable. It may break in case of accented or foreign letters due to the way HFS encodes accented characters (as decomposed (composing accent character)(simple letter) pairs). This might seem stupid but it's not -- trust me, you don't want to get angry e-mail in French :) Use NSFileManager's -fileSystemRepresentationWithPath: instead.

----

The reason I use this code instead of NSWorkspace's methods is I have found that NSWorkspace's methods often return networked volumes (yes, even mountedRemovableMedia does sometimes, that's why it's unstable), and then if you're checking to see if a file exists on a networked volume (which will never be the iPod), it may slow the system down significantly. That is where NSWorkspace is flawed for finding iPods. --KevinWojniak

