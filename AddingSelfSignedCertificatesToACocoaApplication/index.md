---
layout: page
title: AddingSelfSignedCertificatesToACocoaApplication
---

I'm working on a Cocoa client program that talks to a webserver. Up until now, I've been using HTTP to communicate with said server, but I'd like to switch over to HTTPS.

I've been pulling information off the webserver primarily by:

a) Using     +[NSURL URLWithString:] to create URLs with the string, @"http://myserver.com/"

b) Retrieving the information through the method     +[NSString stringWithContentsOfURL:encoding:error:] ...


So I figured switching to HTTPS would be a simple question of making the necessary server adjustments and changing the aforementioned @"http://myserver.com" string to @"https://myserver.com" ... What I DIDN'T realize is that Cocoa forbids me from accessing data over HTTPS if the server being accessed uses a self-signed certificate.

Having talked to friends / read up a bit on the subject, I now know that Cocoa's restriction is imposed as a security precaution to prevent man-in-the-middle-attacks. With that in mind, however, I still need to know how I can distribute my own public key with my Cocoa application and how I can tell Cocoa that, "hey, it's OK to accept certificates signed by me in the context of this application." It looks like this may be possible through the use of CSSM, but I'm not altogether too sure... so I'm writing this post seeking clarification and hoping I won't need to install any third-party libraries.

----

You'll need to use the new URL access stuff in WebKit to do this. The documentation for WebKit lists a bunch of callbacks you will receive in response to authorization challenges; you should be able to do something there, though it's not quite as good as adding your certificate to the keychain (which is yet another option).

