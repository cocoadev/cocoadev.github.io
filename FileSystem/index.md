---
layout: page
title: FileSystem
---



Every volume has a filesystem type that tells how files and data in that volume are organized, and how the OS accesses the data.

Usually filesytem types go along with operating systems, but often different operating systems get drivers to access other filesystem types for intercompatibility. (As you might expect, Microsoft never does this, as it would validate competitors.)

Linux systems in particular are getting more and more different disk filesystem types for various purposes such as journaling.   It also has the largest collection of drivers for other OS's filesystems. 

On Mac OS hard disk partitions, you get to use these:

HFS+ - recommended for general use, and for all Mac OS X root partitions.  Case insensitive, case preserving.

HFS - a throwback to the old Mac days.  Limited to 2 gigs total.   Case insensitive, case preserving.

UFS (UFSFileSystem) - throwback to the NeXT days.  Case sensitive. 

MSDOS (MSDOSFileSystem) - a windows/msdos partition, either FAT16 or FAT32.   Case insensitive, case preserving, sometimes.

