---
layout: page
title: NSNetServiceBrowser
---

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSNetServiceBrowser.html

Used for Browsing (i.e. discovering RendezVous network services)

(ObjC only and 10.2 and later only.)

The example application that apple provides (PictureSharing and PictureBrowserSharing) are good starts. Here is another useful example to read through:

http://macdevcenter.com/pub/a/mac/2003/05/13/cocoa.html

----

Has anyone else noticed that NSNetServiceBrowser is GOD AWFUL at resolving services found? Almost half the time I search for something, the delegate tries to resolve the service, but it never completes. Anybody else had this trouble?

-- KentSutherland

Have not had that issue at all. Do you have some serious network issues? -- MatPeterson

Here neither, in the development SubEthaEdit we almost never got netService:didNotResolve:. Note that the actual resolving does the NSNetService, and you have to register as delegate for that to recieve the netServiceDidResolveAddress: method. Also note that you should send the NSNetService a stop message after you have sufficiently resolved your stuff, otherwise you cause much unneccesary traffic, which Panther will comment with some NSLog messages to the console. -- DominikWagner

