---
layout: page
title: CreatingDiskImages
---



Does anyone know what the easiest way is to programatically create a Disk Image from a directory? The manual for hdiutil tells how to do it the old style, (using du, and hdiutil), but it says that some of it is now handled by -fs. If anyone could offer any insight, it would be greatly appreciated.
----
Does this help?
http://www.c-command.com/dropdmg/
-- JohnDevor

Hmm... Scrath that idea... I didn't catch the "programatically"

Programaticaly: Using a program :-)

----

When the hdiutil manpage says '-fs does some of this work now', it just means that this:
    
hdid -nomount folder.dmg
newfs_hfs -v myFolderImage /dev/rdisk1s2
hdiutil detach disk1

is now accomplished by passing     -fs HFS+ to hdiutil create.

----

Would it be possible  to use AppleScript to control Disk Copy, to create a disk image?

----

I don't know about doing it 'programmatically'. I do it with a shell script that I use as my final build phase, executed from my main project directory. I call it 'create_dmg' and it goes a little something like this:

    
#!/bin/sh

# invoke this script like: create_dmg image_name "Volume Name"
# it will produce image_name.dmg

# if a previous copy of the image exists, remove it
rm -f build/$1.dmg

# create the image. My software fits on a 5MB image
hdiutil create build/$1.dmg -size 05m -fs HFS+ -volname "$2"

# mount the image and store the device name into dev_handle
dev_handle=`hdid build/$1.dmg | grep Apple_HFS | perl -e '\$_=<>; /^\\/dev\\/(disk.)/; print \$1'`

# copy the software onto the disk
ditto -rsrcFork "build/Product" "/Volumes/$2/Product"

# unmount the volume
hdiutil detach $dev_handle

# compress the image
hdiutil convert build/$1.dmg -format UDZO -o build/$1.udzo.dmg

# remove the uncompressed image
rm -f build/$1.dmg

# move the compressed image to take its place
mv build/$1.udzo.dmg build/$1.dmg


-- TravM

----

Along the same lines, anybody got a convenient way, from a build script, to attach a license agreement to a disk image? I think it has something to do with resource forks, but I'm not really sure. I use DropDmg right now to attach big "THIS IS PRERELEASE SOFTWARE" warnings to all the beta stuff I release, but I'd rather do it as an automatic part of the build process.

(JeffHarrell)

You can use the DropDMG command-line tool from a build script.

--MichaelTsai

----

Not sure how to do it from a script, but this page links to the UDIF SLA SDK: http://developer.apple.com/sdk/index.html

----

Why oh why do you want to waste your users time by showing a license agreement that he won't read??? So that your software seems professional?

--

When your software corrupts the user's important files and the user sues you, you can point out that the user explicitly agreed that you were not responsible for any damage.

--

"Why oh why do you want to waste your users time by showing a license agreement that he won't read???"

When you throw 36-point bright-red type on the user's screen saying "THIS IS PRERELEASE SOFTWARE," he reads it.

(JeffHarrell)

Perhaps a bit off topic considering the title, but does it really make a legal difference?  Is showing a license agreement when you download the software better than simply putting the license agreement in a readme?  And finally: is this something developers should worry about?

--

License agreements aren't just for "don't sue us" purposes. I include a license agreement that covers the source code and where to get it.

----

This is a nice script - that allows licences: 
http://www.objectpark.org/buildDMG.html - DiggoryLaycock

----

Does anyone know how to change the volume name of a read/write disk image once it has been created? 

----

The same way you change the name of any volume. Mount the disk image, select the drive on your desktop, hit Enter. Unmount the image when you're done.

----

OK great, that can be AppleScripted and used with osascript, I don't know why the simple answer didn't come to me.  Still I  would prefer a command line method for changing the volume name, AppleScript is tedious.

----

Err, never mind I can use diskutil to change a volume name.

