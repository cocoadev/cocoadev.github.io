---
layout: page
title: MSDOSFileSystem
---



A decrepit FileSystem that is agonizingly pervasive.
Compact Flash and Smart Media flashcards for digital cameras use MSDOS filesystems, for instance.

These filesystems usually come about because of the existence nearby of a Wintel machine.
Anyway, Mac os X and 9 etc have all been able to mount them, just fine, and read and write to them,
whether on floppys, ZIP disks, or a hard disk you connect via scsi or firewire. 

There's several major kinds; as the years wear on, old kinds are abandoned in favor of the newer ones:

FAT32 - FAT16 upgraded to get bigger than 2 gigs, and for smaller cluster sizes.  kindof like with hfs+.
Currently the favorite in the Wintel world.

FAT16 - kindof like the old HFS format for macs; limited to 2 gigs and a huge cluster size.

FAT16 less than 32 megs - a format they used before it occurred to MS that someone might want to have a disk with more than 32 megs on it.

FAT12 - whoa, really old.  I think this is still used for like floppy disks.

Originally, FAT filenames needed to follow the 8.3 rule, that is: the filename consists of the Name and the
Extension.  The Name is 1 to 8 characters.  The extension is 0 to 3 characters.  There is always a
dot between them, but the dot is not actually a part of the file name. Therefore you can make a file named "dog" (Name "dog" and empty extention) but you cannot make a file named "dog." or ".dog" - these will give syntax errors.

The filenames were stored casefolded.  Various kinds of software, most often from the unix world,
would lowercase all of the letters, then other software, by PC geeks, would uppercase it again.
Current versions of Windows, in some places, mindlessly Capitalize them.
Back in those days, it was OK, everything at least worked within the rules.

Starting with Windows 95, microsoft added an ugly hack to sortof kindof support longer filenames, with case retention.  It's ugly, this guy described it to me once.  Say you have a file named "billGatesIzaTwerp.text"
- not 8.3!!  It actually gets stored in a file named "BILLGA~1.TEX" or something like that.  Then,
secret filesystem entries are added that basically say

billGatesIzaTwerp.text = BILLGA~1.TEX

Older software ignores the secret entries, so all they see is a file named BILLGA~1.TEX.
I think the entries have some delete bit set or something so that people ignore it.
But newer software picks up the secret entries and tries its best to pretend it's OK.

Then, the next file to be stored in the same directory, say with the name billGatesIzaDork.text
becomes BILLGA~2.TEX - they number up like that.

Some bugs with this scheme:

Try adding another file named BILLGA~1.TEX to your directory.  Ha.
Now try to formulate a meaningful error message to tell the Luser.  Ha.

You are limited to like 100 files that start with the same 6 characters.
This is because once the file system has made files all the way up to BILLGA~9.TEX, it will drop one character from the name and continue with BILLG~10.TEX, and so on up to BILLG~99.TEX.
This has devastating effects on Javadoc files, which often have names something like:

com.mycompany.mydivision.Classone.html

com.mycompany.mydivision.Classtwo.html

Also, you will find that filenames that do follow the 8.3 rule get case-ambiguated,
because they are still stored in the old way.
A directory named Monopoly will appear to change to MONOPOLY or monopoly
depending on who you ask.  But the directory Monopolies won't have this problem.

----

Thanks a ton for that last tidbit. I was going insane trying to figure out why some of the file names on my FAT-formatted USB flash drive were showing up uppercased on my Mac. Is there a way around this (short of renaming each file to break the 8.3 rule)? If NTFS doesn't have this problem, I might give that a try.

----

Joke

Judge Kathleen Kottler-Kavein ruled that Microsoft must be broken up into two companies.
The name of the two companies will be MICROS~1 and MICROS~2.

