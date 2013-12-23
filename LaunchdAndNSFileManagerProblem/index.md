---
layout: page
title: LaunchdAndNSFileManagerProblem
---

What I'm trying to do:

launchd tells me when /Volumes has changed, so I can find out when my GPSr is plugged in to the computer. (It's a Magellan eXplorist, so it creates a nifty file system on the desktop.)

I've made a command-line tool that links to Foundation and AppKit (for NSWorkspace, but that's not relevant to the problem).

In response to launchd, the tool uses NSFileManager to iterate through the contents of /Volume until it comes up with [fileManager fileExistsAtPath: ..special path that only exists on my GPS...].

My code works just fine in Xcode and as a stand-alone in the Terminal. But launched from launchd, it never finds the special file.

Anyone care to guess why that might be? Here's some code... I'm using printf because launchd really does not like NSLog at all. :-)

    
	//OK, first we grab the file manager...
	NSFileManager * fm = [NSFileManager defaultManager];

	// Get an array of all the volume names. Does this work in all languages?
	NSArray * allTheVolumes = [fm directoryContentsAtPath:@"/Volumes"];

	// Iterate through the volumes until we find one that looks right
	// The user could rename the volume, so we'll go by the USBTRANS directory and its contents
	NSEnumerator * volumeEnumerator = [allTheVolumes objectEnumerator];
	NSString * volumeName;
	NSString * checkfilePath; // /Volumes/<volname>/USBTRANS/UNIT_ID.DAT
	while ((volumeName = [volumeEnumerator nextObject]))
		{
		checkfilePath = [NSString stringWithFormat:@"/Volumes/%@/USBTRANS/UNIT_ID.DAT", volumeName];
		printf("checking: %s\n", [checkfilePath UTF8String]);
		if ([fm fileExistsAtPath:checkfilePath])
			{
			printf("%s exists. Launching the app... (which doesn't exist)", [checkfilePath UTF8String]);
			[[NSWorkspace sharedWorkspace] openFile:checkfilePath withApplication:@"TextEdit.app"];
			break;
			}
		}


And here's the launchd plist file, which I constructed with the very excellent Lingon.app:

    
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Debug</key>
	<true/>
	<key>Label</key>
	<string>MagellanVolumeWatcher</string>
	<key>OnDemand</key>
	<true/>
	<key>ProgramArguments</key>
	<array>
		<string>/Users/some_guy/MagellanVolumeWatcher/build/Debug/MagellanVolumeWatcher</string>
	</array>
	<key>StandardOutPath</key>
	<string>/Users/some_guy/magellanout.log</string>
	<key>TimeOut</key>
	<integer>60</integer>
	<key>WatchPaths</key>
	<array>
		<string>/Volumes</string>
	</array>
</dict>
</plist>


Any help appreciated.

----

Have you tried compiling in release?  At the very least make sure you've disabled ZeroLink.

You also have to remember that launchd runs in the root context, not a user login context.  I'm actually not sure NSFileManager will work outside of a login context.  You might want to look into using DiskArbitration instead.

----
Since you're opening the file in question in TextEdit, you probably don't need this to work unless you're actually logged in. A much simpler approach would then be to use NSWorkspace notifications to watch for newly mounted disks, NSWorkspace methods to enumerate mounted disks, and run the whole thing as an LSUIElement or LSBackgroundOnly app that gets added to your LoginItems so it's running all the time when you're logged in.


----
Using NSWorkspace is actually *not* the simpler approach. It's the much more complicated approach. LaunchD already does the heavy lifting, and my program just responds as a one-off, sort of like FolderActions. I'd be using FolderActions already if it would let you attach stuff to /Volumes. And shoot: You can do this in AppleScript ( http://macscripter.net/articles/440_0_10_0_C/ ), so why not a Foundation tool? 

Compiling as Release and disabling ZeroLink yield no change. Works perfectly from Terminal, fails from launchd. What kind of difference might it make that the volume in question is FAT16?

----
Obviously opinions may vary, but NSWorkspace is a single line of code to listen for volume mounted notifications, versus your large launchd plist above, and the attendant malfunctions it seems to be prompting.

----
OK, I think I got it: LaunchD isn't the problem; Magellan is. The GPS unit presents a USB mass storage device that has no volume name. In the Finder, it shows up as 'Untitled,' but in DiskUtility it shows up as disk1s1. Rename the volume in Finder, and the program works. Unmount and remount, and the volume name of 'Untitled' returns, along with the problem.

One wonders why NSFileManager has trouble with this in the various contexts.

But seriously: LaunchD is cooler than your dad. :-)

----

Here's the equivalent version using NSWorkspace in a Foundation tool:

    
@implementation MyListener
{
    - (void)startListening
    {
        [[NSWorskpace notificationCenter] addObserver:self selector:@selector(didMount:) name:NSWorkspaceDidMountNotification object:nil];
    }

    - didMount:(NSNotification *)notification
    {
        NSString *checkFile = [[notification userInfo] objectForKey:@"[[NSDevicePath"] stringByAppendingPathComponent:@"USBTRANS"] stringByAppendingPathComponent:@"UNIT_ID.DAT"];

        if([[NSFileManager defaultManager] fileExistsAtPath:checkFile])
            [[NSWorkspace sharedWorkspace] openFile:checkFile withApplication:@"TextEdit.app"];
    }
}


So which is easier, again?

----

Neither One Works To Do The Job I'm Trying To Do, Because Something Else Is Broken.

----

You said that it was broken because the mass storage device presents itself as an untitled volume.  I'm reasonably sure this is because you hardcoded "/Volumes/%@/..." into your version of the app.  You noticed that Disk Utility is showing the device is disk1s1; this is the BSD name of the device (not the mountpoint), which Disk Utility falls back on.  You'll notice the version I posted above waits for a notification from NSWorkspace and uses the mountpoint provided.  There is no hardcoding of anything other than the "USBTRANS/UNIT_ID.DAT" path suffix which you're using to test that the device is your GPS unit.

Assuming that the automounter actually creates the mountpoint somewhere on the system (it could even be in /private, which is what happens for network mounts) the code above will work.  Yours will not.

----

Like I said: Neither one works. I did an NSWorkspace version where I asked NSWorkspace for mounted removable volumes, and it still didn't find the file on the volume when run as an agent, but did fine when run in Xcode or Terminal.

----
Can you clarify what is wrong... is your program actually being launched by Launchd? ...what is the debugging (printf) output - list of volumes it can see, that NSWorkSpace and NSFileManager are valid... the return code from the openFile:withApplication...

----
As an agent, or as a daemon?  I'm assuming you read http://developer.apple.com/technotes/tn2005/tn2083.html and really did mean agent.  If you ran it as a daemon, it could be a result of how the automounter sets permissions on the mounted volume.  Similar problems exist on Linux with udevd.

----
Yes, as an agent. One of the reasons I initially avoided using NSWorkspace was because that document you linked to says Foundation is daemon-safe whereas AppKit is not. Of course, it doesn't matter as much because it's an agent and not a daemon.

And yes, for the fifth time :-) , my program is being launched by launchd in response to changes in /Volume. What is specifically wrong is that when launchd launches it, it is unable to find a file that exists on one of the mounted volumes. When *I* launch it (through Terminal or Xcode) it finds the file. I've tweaked it to use nested stringByAppendingPathComponent messages, I've tried using NSWorkspace's mountedRemovableVolumes (or whatever it is), and still the problem persists.

I'm beginning to think it's Apple's problem. :-) Agents are supposed to run in user-space, so if the Finder can show it to me, and if running it in bash can show it to me, then running it as an agent should show it to me.

----
I think we understand the generalities of what's wrong, what we're looking for is specifics. What do your print statements actually print? Does the thing not find the volume at all, or does it find the volume but not the file? What happens if you try to list the contents of the volume? There's a lot more information which could be gathered and we're going to have a hard time helping you with the small amount you have provided.

----

OK... Here's a sample result when run from Xcode:
    
[Session started at 2007-07-18 12:56:14 -0700.]
checking: /Volumes/MacintoshHD/USBTRANS/UNIT_ID.DAT
checking: /Volumes/Untitled/USBTRANS/UNIT_ID.DAT
/Volumes/Untitled/USBTRANS/UNIT_ID.DAT exists. Launching the app... (which doesn't exist)

MagellanVolumeWatcher has exited with status 0.


And here's what happens in launchd, seen through Console.app, via a stdout log set up in the launchd plist, after an eject and a remount. (The device remounts itself automatically whenever it's ejected, which is a little bit annoying for an end-user, but kind of handy for debugging.) Note that it doesn't say the file was found:
    
checking: /Volumes/MacintoshHD/USBTRANS/UNIT_ID.DAT
checking: /Volumes/Untitled/USBTRANS/UNIT_ID.DAT



And, finally:
    
blackbook:~ some_guy$ ls -al /Volumes/Untitled/USBTRANS
total 160
drwxrwxrwx   1 some_guy  some_guy  16384 Jul 15 21:13 .
drwxrwxrwx   1 some_guy  some_guy   8192 Dec 31  1979 ..
-rwxrwxrwx   1 some_guy  some_guy    130 Jul 18 19:55 LANGUAGE.DAT
-rwxrwxrwx   1 some_guy  some_guy    178 Jul 18 19:55 POI.DAT
-rwxrwxrwx   1 some_guy  some_guy    220 Jun 18 13:44 UNIT_ID.DAT
blackbook:~ some_guy$ 



Does this yield anything useful?

----
A directory listing of Untitled or USBTRANS may be useful. But I already have a theory.... Mounting a volume in /Volumes is a two-step process, where first a directory is created and second the disk is mounted there. My theory is that your code is running too fast, that it's running after the Untitled directory is created but before the disk is actually mounted on it. You find nothing because in fact there isn't anything there yet, and it works from Xcode because the disk has had time to mount. What happens if you sleep for ten seconds before running the check?

----
Hah! sleep(1); does it. :-) Can't use NSTimer or any runloop -oriented code in a command-line tool.

----
Of course you can, as long as your command-line tool is actually running a runloop, and there's no reason it can't.

Anyway, a sleep(1) is not very reliable since you don't know how long the mount operation will really take. This is another place where the NSWorkspace notifications would be useful. Otherwise, I would poll the location at some decent interval until you can see that there is actually a disk mounted there.

----

Try [NSThread  sleepUntilDate:[NSDate dateWithTimeIntervalSinceNow:1.0]]

Glad you got this sorted, finally.

----

Thanks, I need something like this, but have a question.
Launchd can keep an eye on more then one path. Is there a way that launchd can pass the path or any other info to the deamon?

