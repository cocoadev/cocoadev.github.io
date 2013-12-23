---
layout: page
title: FileSystemNotifications
---



Filesystem notifications is the mechanism that Mac OS X and the Finder uses to keep an updated view of the filesystem. In Mac OS 9, the Finder conducted a lot of polling, to check for changes in the file system. This approach was dropped in X, probably for performance reasons. It is now the responsibility of all processes to notify each other of changes (file creations, deletions, etc).

There is API for FN both in Carbon and in Cocoa, but the Cocoa API is not complete at the moment.

----
**Cocoa**

In Cocoa, the FN API is part of the NSWorkspace class.
    
- (void)noteFileSystemChanged;
- (void)noteFileSystemChanged:(NSString *)path;
- (BOOL)fileSystemChanged;

    - (void)noteFileSystemChanged is used to tell the system that a massive change was made, to many different locations throughout the file system. It should mainly be used by installers and other programs making many changes in a short time, but is inefficient for notifying a single modification.
    - (void)noteFileSystemChanged:... is used to tell the system that a modification was made to the directory at path.
    - (BOOL)fileSystemChanged is used for polling for changes. It returns true if there has been any file system changes since the last polling.

Many Cocoa classes automatically notify changes to the file system. Those include NSSavePanel and NSDocument.

**WARNING!**

According to Apple's documentation:
[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWorkspace.html]
*- (BOOL)fileSystemChanged

Returns YES if a change to the file system has been registered with a noteFileSystemChanged message since the last fileSystemChanged message; NO otherwise. **Currently always returns NO.**
*

So it looks like you have to use the Carbon approach for now.

----
**Carbon**

Carbon provides some additional functionality related to polling. The API is described in <Carbon/Files.h> and can be used without a problem from a Cocoa application (if Carbon.framework is included).

    FNNotify(
  const FSRef *  ref,
  FNMessage      message,
  OptionBits     flags)

FNNotify() is used to tell the system a change was made. The only documentation that exists seems to be embedded into the Files.h file.

    FNNotifyByPath(
  const UInt8 *  path,
  FNMessage      message,
  OptionBits     flags)

Alternative to FNNotify().

    FNNotifyAll(
  FNMessage    message,
  OptionBits   flags)

Notify of massive changes.

    FNSubscribe(
  const FSRef *        directoryRef,
  FNSubscriptionUPP    callback,
  void *               refcon,
  OptionBits           flags,
  FNSubscriptionRef *  subscription)

This function is used to request notifications of a particular location. It uses a few different UPP's and structures that are outlined in Files.h.

    FNUnsubscribe(FNSubscriptionRef subscription)

Cancel a subscription.

----
**Cocoa+Carbon**

Because I wanted access to the subscription services that Carbon provides, and avoid doing polling, I created a class to access those functions from a Cocoa interface.

    @interface DRFileNotificationSubscription : NSObject {
    //removed to save space
}

- (id)initForDirectory:(NSString*)path target:(id)newTarget action:(SEL)action;
- (id)initForDirectoryURL:(NSURL*)url target:(id)newTarget action:(SEL)action;

- (void)setTarget:(id)newTarget;
- (void)setAction:(SEL)newAction;

- (id)target;
- (SEL)action;

- (NSString*)watchedPath;
- (NSURL*)watchedURL;

- (void)fire;

@end

Each instance represents a subscription to a particular file system location. Once created, the subscription cannot be moved to another location, and the subscription exists until the object is deallocated. When the subscription receives a notification, it calls its target's action. action is a selector like an IBAction, and (id)sender points to the subscription instance. The sender can be used to figure out what directory was meant ([sender watchedPath]).

You can download my class from http://ittpoi.com/drfns.tgz

One thing I noticed, was that Finder itself is not very good at sending notifications. Notifications are not delivered when your application is in the background either.

Enjoy.

-- DavidRemahl

----
**BSD**

The BSD layer provides a related API that allows monitoring of events associated with individual files:     kqueue(2) [http://developer.apple.com/documentation/Darwin/Reference/ManPages/man2/kqueue.2.html].

UKKQueue implements a simple Cocoa interface to the kqueue file change notification mechanism.

----
**DISCUSSION:**
----

I'm looking for a way to be notified when any file on the filesystem changes. I thought I could use kqueue, but it seems that it only works for specific files (vnodes, actually). My second thought was periodically checking the journal file for recent changes (ugh, polling). This seems like a rather complicated, dangerous, and unelegant solution. If I can find a way to do this, I'm planning on making something similar to locate, but without the need to update the database periodically. Any suggestions?

----

*any file on the filesystem changes????*
This is way too encompassing. I seriously doubt you would want to be notified of every single disk operation.

----
OK, so maybe not QUITE that extensive. Any user visible change (name, location, size, stuff like that) to a user-useful file. Excluding parts of /System, /Library, and other not-so-commonly-used places would be acceptable as well. This is going to be on a background process running at very low priority, so as long as it avoids disk access as much as possible it shouldn't have TOO much affect on anything else running (I hope).

----

Trying to watch for filesystem changes using these types of things (including kqueue) is problematic in many respects, but especially because it almost never works over a network. A good approach if you really need to know about changes to the filesystem (e.g., you're writing a new Finder) is to refresh your views when the user brings one of your windows to the front or clicks on the window. Most users find this intuitive, and it doesn't require polling *or* the use of relatively low-level APIs like kqueue.

It might also be worth looking at SGI's FAM [http://oss.sgi.com/projects/fam/], particularly if you have some time on your hands and can add support for kqueue.

----

Okay, then what is the best way, for instance, to detect changes in the iPhoto library? This what I'm interested in specifically. I'd like my app to trigger a sync routine to check the state of some metadata my app attaches to each album in the library. A simple reload every time the app gains focus won't do here.

Looking at this some more after leaving it for a while, it appears that SGI's FAM does exactly what I want. Does anyone know if this has been successfully ported to OSX? (I know a few people have tried, but I don't know if they succeeded)

----

you might want to have a look at this: [http://greenearthcommons.org/rian/gfslogger/]. There is a gui app (but no source for that) here: [http://www.macs.hw.ac.uk/~rpointon/osx/fseventer.html] that shows it in action. I haven't measured it's performance needs, but it is quite effective (and useful if you want to see what crazy places an installer litters around in). It uses tech that apple put into the kernel to make spotlight work, so it requires tiger.

----

I've got an app that produces sound files. One thing I'm trying to incorporate is that once a file is created, you hit a preview button and a drawer drops down with an NSMovieView that lets you preview the file that was created without having to go into the Finder. The problem is that there seems to be some lag time in OSX's file updates.

For example, you make a file. It previews fine. But then you overwrite it with a new file. If you hit preview quickly once the creation process is done, the file that is loaded into the NSMovieView will actually be the old one as if the OS hasn't updated the file listing yet. If you count a slow 'one-one thousand' and then hit preview, the file will be fine.

To get around this, I included an [NSFileManager removeFileAtPath: handler:] call that is used to delete a file that is slated to be overwritten, with the hope that if the file was deleted, this problem wouldn't occur. No such luck. If the creation process is fast, and you hit preview as soon as its done, you get kind of a halfway file; it's usually the new file, but the wrong duration and with extra static added in. Again, if you count 'one-one thousand' before hitting preview, everything is OK.

Is there some way to get around this? Or force the application to wait until the filesystem updates before allowing a preview?

----

TonyArnold - you'll want to look into kqueue and kevent (specific to Darwin) - I've not used them myself, but they allow kernel level notification of changes to the filesystem (or a specific file in your case). Post back if you work it out.

----

How is your app writing to disc? If you are using an NSFileHandle there is a method to synchronize the file     [audioFileHandle synchronizeFile].

If you are updating your file with     write() then there is a     unistd function that can synchronize your file     fsync(). 

Once your file is synced you can create a new NSMovie with a reference to the file and set the movie to your movie view. --zootbobbalu

----

Well, it's a wrapper so I have no control over how the file is getting written. I tried just making an NSFileHandle for reading to the path and syncing it, but that didn't work. As for the kevent() and kqueue() calls...even reading those man pages, I'm a little confused about them (especially kevent(): it has 2 arrays of kevents?).

----

It sounds as though the NSFileHandle, the cocoa file management layer, or QuickTime is keeping the file open for a while after the access since the filesystem layer in the OS core will be doing this correctly.  Have you tried sending the closeFile message to the file handle for the movie before trying to re-open it?  That should make sure that no component of the library is still holding the descriptor (since that would still point to the overwritten file until closed).

----

OK, just so I am clear, there is no reliable way to have the OS notify me (by invoking a callback function, or sending an NSNotification) when a file is written to.  kqueues involves polling, albeit efficient polling. And the CarbonFNSubscribe functions only work if the process that modifies the file is savvy enough to mention it via an FNNotify.

----
How do kqueues involve polling? I thought the whole point of kqueues was to avoid polling.

----
FNSubscribe works fine in Tiger even with non-savvy apps. I wrote a wrapper class that might do what you want--see my personal page (JonathanGrynspan) for GTSubscription.
----
I tried using GTSubscription (basically reusing the example code on GTSubscription website). It works nicely when I use Finder to modify the folder (~/Library/Application Support/iCal), however, if i modify a file using e.g vi, it doesn't recognize changes. Nor does it recognize changes made to the nodes.plist file (by iCal) even though Finder updates itself every minute (approx) (The modified attribute reflects this) - Saptarshi

----
Leopard now provides the FSEvents API - this can be used for getting notification of directory changes including the entire filesystem (by looking for changes beneath "/"). Sample cocoa code demonstrating this is as http://www.fernlightning.com/doku.php?id=software:fseventer:leopard

----
GTSubscription only works with directories because it is based on the Carbon FNSubscribe function. This is not a bug in GTSubscription. -JonathanGrynspan

----
I notice that http://ittpoi.com/drfns.tgz from above is not available anymore. I like to see if I can use it. Does someone knows where I can find it or got a copy for me? lordanubis @ xs4all.nl

----
The Wayback Machine is your friend. http://web.archive.org/web/20070708130924/www.ittpoi.com/ichatstatus/

----
There is now a modernized, faster, more efficient version of UKKQueue called "VDKQueue". It is available here: http://github.com/bdkjones/VDKQueue

