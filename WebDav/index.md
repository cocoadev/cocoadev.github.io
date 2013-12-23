---
layout: page
title: WebDav
---

General/WebDAV stands for "Web-based Distributed Authoring and Versioning". It is a set of extensions to the HTTP protocol which allows users to collaboratively edit and manage files on remote web servers. See http://www.webdav.org/ for more details.

Apple uses the webDav protocol for iDisk.

I have several questions about webDav and cocoa.

What is the best way to upload files to a file to someone's .mac account? Is it best (or necessary even?) to mount the webDav volume and then copy files into it?

How can one programatically mount an iDisk? Using the command line utility     mount_webdav works, but is there a way to pass the username and password to it to prevent a dialog box from popping up? (You can give it the path to a file containing the username and password, see 'man mount_webdav' for details. )

* You could make a gui to wrap the cadaver utility ... look at the cocoa Dev central website *

It is possible to obtain the current user's iDisk/iTools username using the command
    General/NSString* username = General/[[NSUserDefaults standardUserDefaults] objectForKey : @"iToolsMember"];
Does anyone have working code for obtaining the user's iTools password? (I assume it's stored in the Keychain)

There currently seems to be no resource containing this information on the web, so hopefully we can create one here!

*nobody knows about webDav?*

----

There is a private framework in 10.3 I believe that lets you access iDisk accounts. do a search for DAV in your General/PrivateFrameworks folder, and then view the headers.

*There is "General/DAVKit" and looking at the output of nm, it seems to be the real stuff. But no header...*

----

Look for the .Mac SDK in the downloads section of your ADC account
