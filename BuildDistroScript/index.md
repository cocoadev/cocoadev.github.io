---
layout: page
title: BuildDistroScript
---

I use a Perl script to help me build distribution versions of LiveDictionary. It builds all of the various pieces and automatically creates a disk image that is ready to be uploaded to the server. It was a bit of work to get it working properly, but it's tremendously useful. Part of this is because LiveDictionary has an extremely complicated build process due to the fact that it consists of a lot of different components that have to be built in a specific order, but I believe a script like this could be useful for more straightforward projects as well, so I'm posting an amended version that you can use as a starting point for your own script. I will intersperse code with commentary. Perl is not my native language so it may be ugly; I mostly use it as a glorified shell script.
    
#!/usr/bin/perl

sub mySystem
{
    ($cmd) = @_;
    print "$cmd\n";
    system($cmd) == 0 or die "System failed!";
}

This is just a small wrapper around the system function that prints the command to run and does error checking. The printing allows you to see the progress of the build and understand what happened when it dies.
    
$buildstyle = "Development";

$ld_directory = "/Users/mikeash/Development/LiveDict";
$build_products_directory = "/Users/mikeash/Development-build-products";

These are just some useful variables. The build style should probably be "Deployment" for releases.     $ld_directory is the location of my source code, and     $build_products_directory is the location of my build products directory (duh) so that the script knows where everything is and will be put.
    
# build helper app
print "-----Building main app, first pass\n";
mySystem("cd $ld_directory; xcodebuild -buildstyle $buildstyle ZERO_LINK=NO");

Now we actually build the app. The real build script does several xcodebuild passes to get everything built properly, but a more normal app wouldn't need them. I've deleted the other ones, since they're not necessary to see.
    
# extract the version info from the infoplist
open(INFOPLIST, "$build_products_directory/LiveDictInputManager.inputManager/LiveDictInputManager.bundle/Contents/Info.plist") or die "Can't open Info.plist: $!";

while(<INFOPLIST>)
{
    if($_ =~ /CFBundleVersion/)
    {
        <INFOPLIST> =~ />(.*)</;
        $version = $1;
    }
}

$version or die "Couldn't get version number";

print "version = $version\n";

This code is a hack to extract the current version number of the app from its Info.plist file. It's highly dependent on the actual textual structure of the plist (as opposed to the XML structure), and so changes in that format could easily break it, but it works fine on current systems. The version number is used to name the disk image, and it's automatically inserted into the readme.
    
print "-----Creating DMG\n";

@filesToInstall = ("LiveDictionary\\ Installer.app", "WordNetAdapter.livedict", "UltraLinguaAdapter.livedict");

$totalSize = 0;
foreach $file (@filesToInstall)
{
    ($sizeString) = `du -k -d0 $build_products_directory/$file`;
    $sizeString =~ /([0-9]+)[\s].*/;
    $size = $1;
    $totalSize += $size;
}

$imageSize = $totalSize*2 + 5*1024*2; #overestimate size by 5MB

Before we can make a disk image, we need to have some idea of how large the disk image should be. Compression will be very effective on empty spots, so overestimating the needed space is not a big deal. I overestimate by 5MB just to make sure there's enough space for miscellaneous small files, and for overhead.
    
system("rm $build_products_directory/LiveDicttmp.dmg");
@hdiutilOutput = `hdiutil create -sectors $imageSize -volname LiveDictionary -attach -fs HFS+ $build_products_directory/LiveDicttmp.dmg`;

foreach (@hdiutilOutput)
{
    if($_ =~ /Apple_HFS[\s]*([^\s].*[^\s])[\s]*$/)
    {
        $diskLocation = $1;
    }
}

$diskLocation or die "Couldn't parse hdiutil output";

We create a new disk image that we can copy files to. The     foreach loop is a way for the script to automatically figure out where the disk image has been mounted, as hdiutil returns that information after creating the image. Normally an image will be mounted at /Volumes/ImageName, but it's a bad idea to rely on that, as lots of things (such as already having an image mounted with the same name, which can happen a lot in this case) can change the mount location.
    
mySystem("cp -r $build_products_directory/LiveDictionary\\ Installer.app \"$diskLocation\"");
mySystem("cp -r $build_products_directory/WordNetAdapter.livedict \"$diskLocation\"/LiveDictionary\\ Installer.app/Contents/Resources/");
mySystem("cp -r $build_products_directory/UltraLinguaAdapter.livedict \"$diskLocation\"/LiveDictionary\\ Installer.app/Contents/Resources/");

Now we copy some files to the disk image. Everything has to go in different places, so I don't use the @filesToInstall list from above.
    
($date) = `date +\"\%d \%B \%Y\"`;
chomp $date;
$sedcommand = "sed 's/__VERSION__/$version/g
s/__DATE__/$date/g'";

$systemString = $sedcommand . "< $ld_directory/README.html > \"$diskLocation/README.html\"";
mySystem($systemString);

This bit of hackery gets the current version and date into my readme file. The readme in my development directory contains __VERSION__ and __DATE__ where I want them to go, and this sed command simultaneously replaces them with the real version and date, and copies the result to the disk image. It's strange to use sed from Perl since Perl is so good at this kind of thing, but it works.
    
$windowScript = 'tell application "Finder"
    set theDisk to POSIX file "' . $diskLocation . '"
    open theDisk
    set theWindow to container window of disk theDisk
    set toolbar visible of theWindow to false
    set current view of theWindow to icon view

    set icon size of icon view options of theWindow to 128
    set zoomed of theWindow to true
    
    set theBounds to bounds of theWindow
    set item 4 of theBounds to (item 4 of theBounds) + 20
    set bounds of theWindow to theBounds
    
    eject theDisk
end tell';

system("osascript -e '$windowScript'") == 0 or die "setting window attributes in Finder failed";

One thing you can't do directly with UNIX commands is position the icons and the window in the Finder, so I embedded an AppleScript that sets the icon size, positions, and window size as I desire them, and execute it with the     osascript command. This allows the process to be completely automated, although it requires that you actually be logged in to the GUI at the time.
    
print "-----Converting DMG\n";
system("rm $build_products_directory/LiveDictionary" . $version . ".dmg");
mySystem("hdiutil convert $build_products_directory/LiveDicttmp.dmg -format UDZO -o $build_products_directory/LiveDictionary" . $version . ".dmg");
system("rm $build_products_directory/LiveDicttmp.dmg");

Now we convert the temporary disk image to a compressed disk image suitable for distribution. The command also inserts the version number into the disk image's name.
    
print "-----Done building.\n";
system("osascript -e 'tell app \"Finder\" to select POSIX file \"$build_products_directory/LiveDictionary" . $version . ".dmg\"'");

And we're all finished; I tell the Finder to show the newly-created disk image just for fun.

I hope somebody finds this script useful. It makes my life a lot easier around release time; I've used this script so many times that I know it works, so I don't have to worry about whether my disk image was properly made and whether everything came out ok. -- MikeAsh

Mike - Thanks for sharing this script!  I modifed it for for own use and it works great, except for the applescript parts.  I'm using 10.4 and the first applescript fails when theWindow zoomed is set to true.  Removing this line allows the script to complete but the bounds are not updated and the disk is not ejected.

The second applescript fails with the message that a string is expected but the end of the script occurred.  Any pointers to some good applescript sites to help me track down the problems. -- CMSCoder

----
Good thing you're not a PMSCoder hehe ;-)

----
I come from Ceeonia, so my native language is C++, while most of the thugs and polititians speak Managed C++, the 'cool kidz' speak Objecive-C, and the rebels speak a dialect called D. A lot of old timers speak with a funny accent, they call it C. Seriously though, few people has ever seen perl that is not ugly, great script! -JeremyJurksztowicz

