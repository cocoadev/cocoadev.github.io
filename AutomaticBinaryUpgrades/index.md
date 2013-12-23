---
layout: page
title: AutomaticBinaryUpgrades
---



Q: I would like my Cocoa app to be able to upgrade itself automatically.  Specifcally, I want that when the program starts up, it connects to the web to check for updates.  If it finds one, then it performs the binary patch and relaunches under the new version.  If it cannot connect to the internet, or cannot for some other reason get the updated version, then the program will not be able to run.  In other words, I will have a guarantee that all of my users are running the most current version, and thus I won't have to support multiple versions.  Does anyone know how to do this?  Can you give me some pointers, or direct me to a tutorial or documentation?  Thanks!

----

If your app is small enough, you can just download the executable and replace it in the executable. (include standard disclaimers about users running your app read-only, like on a CD, read-only dmg, or if the app is located in a directory where they don't have read permissions).   Updating your app piecemeal, like replacing a couple of functions here and there sounds pretty hard, having to pretend to be your own linker.


You could design your app with a central, small, core that never gets updated, and have all your functionality as plug-ins or bundles, which should be pretty easy to replace.  Have your stuff look in the user's library folder for an override plug-in which would 'replace' the original one.  That way if your update screws up stuff, they can just remove the bundles from their home directory to get back to the original version,


----

Q2:  Thanks for your suggestions.  In fact, I do expect my app to be small *and* have very few updates, so downloading the whole new version I don't think will be a problem.  But I still don't see how to do this.  How can a Cocoa program replace itself?  I specifically don't want the user to have to, say, drag the new version to the Applications folder and click yes to "Do you want to replace?"

This plug-in/bundle idea intrigues me.  If it turns out that my app is getting pretty big, I may want to look into this.  Are there standard ways to write Cocoa app plug-ins/bundles?

Thanks again.

----

I wrote a basic self updating application that launches a small application that handles all the updating.

In effect, here is the order:
- User checks for a new update
- Verifies the users requests
- Application start updater app, shutdown self
- Updater changes all the files needed (any nib changes, binary changes, image changes, etc)
- Updater launches application and quits self.

If you handle the download in the background  and its small, the downtime for the user can be 10s or less.

----

Okay, so what mechanism do you use to "change all the files"?  Do you just do a shell call using NSTask with maybe a "sudo" and an "rm" in there?

----

Nope. NSFileManager calls.

----

Hmmm... it probably wouldn't be too difficult to also archive the old files and offer a 'Revert to previous version' option, like the iChat AV beta did.

----
It would be pretty nifty if someone out there wrote a framework and helper app that did all of this so the Cocoa community could have a semi-standard way of self-updating their applications without the user needing to really do much work. We all know how lazy users can be. ;-)

- MarcWeil

----

Although implementing this would NOT be a guarantee that all users of the app will be running the current version, as the OP wanted. Users without internet access, users behind restrictive firewalls, paranoid users who keep a tcp stack sniffing utility open at all times and when an app connects to do something post 'THIS IS SPYWARE! IT CONNECTED WITHOUT ASKING ME!' on VersionTracker, and so on.

----

I'm the OP, and let me explain a bit.  You are right it's not a guarantee in that sense...but I think it will satisfy my needs: The reason I am so concerned about my users always having the newest version is because the client interacts with my sever.  And so, if I make a change, I don't want to have to worry about sending misunderstood messages between client and server.  So, true I can't assume that the user always has a working internet connection, but I can make it so that *when they do connect*, I will not let them truly communicate with the server until they've upgraded.

----

The Sparkle Framework at http://www.andymatuschak.org/pages/sparkle can perform update checking and automatic install like the original post describes.  Free and open source.

----

But it requires 10.4 and adds almost 2MB of bloat... **If it does the job, and well, you can't call it bloat!!**

----

It shouldn't require 10.4... and the bloat is only due to localizations. You can delete the ones you don't use.

