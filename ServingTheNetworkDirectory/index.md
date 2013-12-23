---
layout: page
title: ServingTheNetworkDirectory
---

This topic is not very Cocoa-related; it's more of a MacOSX power user article. However, it's nice and informative and may be of interest to Cocoa developers. As a professional software developer, I personally use my     /Network directory a lot... I frequently switch between machines for testing purposes, and having the right server setup makes it easy to keep the same environment across machines.

----

Ever wondered how the     /Network directory works? You may have seen references to things like     /Network/Applications or     /Network/Users or     /Network/Library in various parts of the Apple documentation.

Well,     /Network is (sort of) just an ordinary directory, which you can put static mounts into. It's only sort of ordinary, because the automounter does certain special things there. For example, it automatically creates the "Servers" subdirectory and populates it with links to any mounted servers. But in general you can set up a machine to mount certain server volumes there automatically.

I've only done it with NFS, so that's what I'll discuss here. The reason NFS is useful is because things in     /Network are only mounted once, at startup. So it helps that NFS obeys the Unix permissions model... you can simply mount it once and each user will have appropriate permissions based on their UIDs. 

These instructions are fairly straightforward for anyone who's ever done server admin work before. If that doesn't describe you, you might want to reconsider doing this ... is it really worth it to you? As usual, there is a small amount of risk if something goes wrong, or if you (or I) make a typo. I believe these instructions to work and I've tried to make them difficult to screw up, but try them at your own risk.

You'll need two things:


*An NFS server. This does not need to be a MacOSXServer, or even a MacOSX machine... you simply need anything that can serve NFS volumes. Set up a directory with applications in it, and export it via NFS.

If you choose to make it a MacOSX machine, here's a template for what you might do on the server to set up an export. 

    
% echo "/Volumes/Share/Applications  -maproot=root -network=10.0.1.0 -mask=255.255.255.0"
  | sudo niload exports .
Password:


Note that this is roughly what MY setup looks like. Your network, mask, and share path are most likely different.     /Volumes/Share/Applications is the folder I'm going to serve as     /Network/Applications. The "-network x.x.x.x -mask y.y.y.y" arguments indicate the range of allowable clients. See the exports(5) man page for more examples of this. "-maproot=root" means that I want the "root" account on the client machine to act as "root" on the server volume. If you don't trust all the clients you might prefer "-maproot=none" instead.

To confirm that the changes were made, you can do this, and you should get back what you entered.
    
% nidump exports .
/Volumes/Share/Applications  -maproot=root -network=10.0.1.0 -mask=255.255.255.0


Your server won't start serving this export until nfsd is restarted. The simplest way to do this is to reboot the server. Or for the more technically inclined... if it's already running you can     killall -HUP nfsd, and if it's not running you can run the startup script in     /System/Library/StartupItems/NFS/NFS. Both need to be done as root.

*Some way to modify all the client machines to mount the server. If you have a NetInfo domain set up, you can make the change in the root domain and every machine in the domain will pick it up automatically. If you don't have a NetInfo domain set up, you can just do it for each individual machine manually.

Since I don't want to get too deep into the gory details of setting up a NetInfo domain, here's how you can set up an individual machine manually. Go onto that machine, and do the following:

    
% echo "server.example.org:/Volumes/Share/Applications /Network/Applications nfs bg,rw,intr,ttl=0 0 0"
  | sudo niload fstab .
Password: 


Again, the path and options are the ones for my setup, and yours may be different. "server.example.org" is a resolvable domain name (or IP address) to the server.      /Volumes/Share/Applications is the path on the server which is being shared.     /Network/Applications is where you're going to mount it locally. "bg" means mount it in the background, rather than blocking system startup til it's complete. "rw" means mount it read-write, as opposed to "ro" which means read-only. "intr" means interruptible, in other words you can cancel some operations if the server is unreachable. "ttl=0" prevents the server from being unmounted just because you haven't accessed it in a while.

The simplest way to get your client machine going is to reboot it once you've made this change.


----

If all has gone well, you'll now have a     /Network/Applications directory on each of the clients. You can export other things the same way ...     /Network/Users,     /Network/Documentation,     /Network/Music, etc. If your experiment works with Applications you should be able to get the others set up the same way.

A few notes:

(1) If you decide to serve     /Network/Users and put home directories on it, remove the "bg" flag from the options... otherwise the server will mount in the background, and it might not be mounted by the time a user logs in. Which means that their home directory won't be available either.

(2) LaunchServices will automatically find applications in     /Network/Applications, just like it finds things in     /Applications. You might need to give it a day or so to run its housekeeping tasks before documents belonging to apps in     /Network/Applications will be double-clickable, however.

(3) If you have MacOSXServer, don't use Workgroup Manager to modify the exports or the mounts. Its interface really was not designed for dealing with static mounts like this. It probably won't trash your setup, but it won't give you any useful way of modifying them either. I spent several days trying to get WM in 10.2 to provide static NFS mounts the way I wanted them, and it was like beating my head against the wall ... in slooooow moooootioooon. There might be a way to do it but I couldn't manage it. Finally I returned to the command-line way of doing things that I'd learned in 10.0, and that worked perfectly.

----

