---
layout: page
title: CocoaAppsWithAdminstratorPrivs
---

My Cocoa app would like to read from system files not readable by normal users.

Question one: How does my app (or parts, threads, whatever of my app) get these privileges? This is not intended to be sneaky, I would like it to work just like the installer or similar apps: "You need to be an administrator to use this function, please identify yourself"

On a related note: What is the best practice to separate the (really few lines of) code which need root privs from the rest? Most of the code will do fine in normal user mode, and paranoid as I am I would like it to remain that way.

Any hints would be appreciated!

Alex

----
Apple's documentation:
http://developer.apple.com/techpubs/macosx/CoreTechnologies/securityservices/authorizationservices/authservices.html

Sample code:
http://developer.apple.com/samplecode/Sample_Code/Security/AuthSample.htm
http://www.clichesw.com/themechanger is an app I wrote which uses the recommended method (self restricting, auto-repairing setuid root tool, with some extra things like build-time generated hashes to ensure it has not been tampered with).

-- FinlayDobbie

----
I'm trying to do this too.  I took a look at the theme changer source and it's a bit much for me to decipher.  Any chance you could post some simplified sample code Finlay?  Thanks,

-JacobMarienthal

----
I think it may be helpful if you checkout BLAuthentication which can be found from the ObjectLibrary here on CocoaDev.
GormanChristian

Yes, I've used that. I found it capable and useful, and this from a person who hates using code other people wrote (Cocoa is only an exception cos it's so pretty-like. I'd make my own hardware if I could...). -- RobRix

----
How would this be extended across a network?  Mac OS X Server has numerous tools that ask you for a user name and password for a server to administrate it from a remote client.  The dialog box on the client does not appear to be native to Authentication Services.  I assume that the client is sending the user name and password across the network (in clear text?) to the server manager daemon.  Then the server manager daemon might use Auth Services.

I can't really find any way for one to programmatically query Auth Services with a name and password not entered directly by the user.  Anybody have any clues?

DaveW

----

BLAuthentication encourages you to do things badly. Read the Apple docs.
As far as OS X Server goes, it may well be using Directory Services or similar (there are other APIs for checking passwords, for example the private CheckPassword.h :)) -- FinlayDobbie

