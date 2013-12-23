---
layout: page
title: LaunchServices
---



LaunchServices is responsible for binding documents to applications and the system-level functionality of launching applications.

It is part of the ApplicationServices umbrella framework.

Documentation:

*Conceptual docs at http://developer.apple.com/documentation/Carbon/Conceptual/LaunchServicesConcepts/index.html
*Reference docs at http://developer.apple.com/documentation/Carbon/Reference/LaunchServicesReference/index.html
*A technote at http://developer.apple.com/technotes/tn/tn2017.html
*The header file at file:///System/Library/Frameworks/ApplicationServices.framework/Frameworks/LaunchServices.framework/Headers/LaunchServices.h


See also HowToRegisterURLHandler.

----



Courtesy of Charles Srstka, on Apple's cocoa-dev mailing list:

These two lines of code will get you an NSArray filled with an NSURL for each application on the system, wherever it may be stored.
    
NSArray *urls;
_LSCopyAllApplicationURLs(&urls);


Unfortunately, the _LSCopyAllApplicationURLs() function is private and undocumented. But it does work, and since it's usually reading cached information out of the LaunchServices database, it's much faster than a file search.

There is more discussion of this under AllApplications

----

Well, the good news is that it is not totally undocumented. Tech Note 2029 does mention it. ;-)

http://developer.apple.com/technotes/tn/tn2029.html

----

New in Mac OS X 10.3 is a promising concept called UniformTypeIdentifiers (UTIs), which serve much the same purpose as MIME types and appear to be designed to replace OSTypes.

See LaunchServices/UTType.h for more information. *--boredzo*

----

I would like to associate my document type (.score) with my Application.  That means I would like the documents created by my application to have my Application Icon.  I would also like to launch my Application when a .score document is double clicked in the finder and open that  document.
Thanks for any input

----

To associate your document and application, modify the application's Info.plist file. You need to add a 'CFBundleDocumentTypes' entry. The link below will get you started. Also, look at the Sketch AppKit example.

http://developer.apple.com/documentation/MacOSX/Conceptual/BPRuntimeConfig/Articles/PListKeys.html#//apple_ref/doc/uid/20001431-101685

file:///Developer/Examples/AppKit/Sketch/Info.plist

-- GrahamMiln
----
Thank you for your reply. The 'CFBundleDocumentTypes' entry was quickly added in the active target configuration.
What about launching a document that's associated with my application from the finder?  Do I need to implement a certain message signature somewhere in my app to take arguments from the finder?
Thanks again

