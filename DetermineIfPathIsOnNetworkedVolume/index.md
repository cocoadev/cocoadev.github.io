---
layout: page
title: DetermineIfPathIsOnNetworkedVolume
---



Given any path, is there a way to determine if it's on a network volume (i.e. iDisk)?
----
I've been trying to figure this out myself for a project I'm working on.  I thought I could simply check the path for the "/Volumes" directory, but that doesn't distinguish between other local disks and mounted disks.

The only solution I've come up with thus far uses Spotlight, and it sketchy as best. (I stumbled across it by accident while [attempting] to use Spotlight searches on mounted volumes.)  Certain attribute keys will never show up in the dictionary Spotlight returns for a file on a network volume.

For instance: on a local volume, the Spotlight data for a file will contain both "kMDItemContentCreationDate" and "kMDItemFSCreationDate".  For a file on a network, only the "kMDItemFSCreationDate" key will be present.  In fact, this holds true for all keys of the form "kMDItemFS*".  If a file has one and not the other, it's most likely on a network (or was created < 1 second ago and Spotlight hasn't indexed it yet).

As I said, sketchy at best.

Edit: I'm using the AppleScript stuff found here, and it's been working fine.
http://www.cocoadev.com/index.pl?GettingNetworkVolumes


----

Why not check the output of the "mount" unix command? that will have the "local" flag for each locally mounted disk, I think. Use NSTask to get the mount command's  output. There is also probably a way to do it properly in the header file sys/mount.h.

----

Have a look at the man pages for: 'getmntinfo' and 'statfs'

----

Look at FSCopyURLForVolume. Returns an AFP / HTTP / SMB /... URL if on an network volume. Use - [NSURL isFileURL] in order to see if it's on a local volume.

You can retrieve volume of a Path using FSGetCatalogInfo, for example.

Also, in order to get an FSRef from a NSURL / CFURL, use CFURLGetFSRef.

%%% 

----
I'm about to get into this same issue.  Has anyone tried using NSWorkspace to get all volumes then comparing that list with [workspace mountedLocalVolumePaths]?  It looks like that will give every drive and disk which is connected "locally" to your computer.  Theoretically that would leave non-local drives.  If nobody tests this I'll let you know in a few days.

-Stephen

----
The way I did it was to go through each volume (from     FSGetVolumeInfo), check if it was a network volume by looking at the information provided by     PBHGetVolParmsSync (see the Tech Q&A NW09: http://developer.apple.com/qa/nw/nw09.html ). If it is, you've got a network volume which you can get the URL of using     CFURLCreateFromFSRef on the information provided earlier by     FSGetVolumeInfo. **Then**  compare that URL with the URL to my file (be sure to watch out for any inconsistencies with trailing slashes, etc.).

I also worked on a method using     getmntinfo, which is a lot simpler if this is all you want to do (ie. I wouldn't recommend it for re-mounting a network volume). Also keep in mind that     getmntinfo stores information about **all** mounted file systems (including local and removable), so any checks on     statfs.f_fstypename will probably be unreliable if new filesystems are added in the future.

----

This is the way I did it, I found the MNT_LOCAL flag in the mount.h header for statfs. So I wrote the following addition for NSWorkspace:

    
#import <sys/param.h>
#import <sys/mount.h>

@implementation NSWorkspace (Extras)

- (BOOL)checkForNetworkMountAtPath:(NSString*)path
{
  struct statfs64 stat;
  
  int err = statfs64([path fileSystemRepresentation], &stat);
  if (err == 0)
  {
    return !(stat.f_flags & MNT_LOCAL);
  }
  return NO;
}

@end


-MattW

