---
layout: page
title: UFSFileSystem
---



	UFS is a General/FileSystem type that is common on
	Solaris and BSD systems (in fact, the native General/FileSystem type there).
It is also usable on Mac OS X and Linux systems.
	

On Mac OS, the disk partitioning program allows you to make either "hfs" or "unix" partitions -
that's UFS, darwin style.
It is possible to use UFS as your root partition, but a lot of things break - in particular,
Classic mode breaks.
Things aren't tested nearly as much as making HFS+ your root partition.

One big difference you'll notice is that filenames are case-sensitive -
you can have files named Abe and ABE and abe all in the same directory,
and they don't collide.
This also means that if your software's looking for a file named "Abe" and the file is really
named "abe" then it won't be found.
This leads to a lot of operational bugs in software.

Another big difference is that resource forks must be stored separately in a filename
that starts with a dot in the same directory.  I forgot the details, it's in the apple docs somewhere.
But, in particular, old style Mac OS 9 software can't see it at all.

Some unix projects will require a UFS partition.  If your make or build procedure
assumes that it can create distinct filenames that only differ in case it will break on HFS+.  I have been told that you can make an on-disk UFS filesystem
with General/DiskCopy, for those times you need to.
Seems easier than allocating some gigabytes to a UFS partition, but people also do
that and it seems to work OK, as long as you avoid using it for things that break.


UFS stands for the Unix File System.  It descended from the Fast File System developed
	by Kirk General/McCusick (also creator of the lovable BSD Daemon mascot) in the groundbreaking Berkeley 4.2 system (4.2 BSD) in the early 1980's.

Unfortunately, the split between BSD and Solaris flavors of UFS happened in the
	1980's, and they are now incompatible and mutually incomprehensible by their respective
	systems.  Mac OS X's UFS is also slightly different, but probably closer to BSD's
	variation.  Linux can often mount any flavor, but read-only.
----

See http://developer.apple.com/techpubs/macosx/Essentials/General/SystemOverview/Finder/The_Finder___Operations.html
for more info on copying files between file systems, including information on ._ files.
