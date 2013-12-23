---
layout: page
title: LinkErrorWithReleaseBuild
---



Hi, everyone.

I have just started Cocoa development using XCode2.1.
My application is to load big binary files (signals from a scientific research tool) and process the data.
I'm currently using Cocoa document based setting with Objective-C.
Now I have 2 questions:

1.  I created myDocument class derived from NSDocument.  It mallocs a bunch of SInt16, UInt32, and float arrays necessary for the processing.  All of them are freed after processing.  The end results are displayed as an instance of myImage class that derived from CIImage (I'm planning to use core image filtering later on).  In Debug mode, my code works perfectly for the first time only.  Whenever I open 2nd or 3rd files, the output looks weird as if it reads totally different location of the memory.  In my understanding, with the document based application, one file is assigned to one instance of myDocument class.  And any new files should be assigned to newly allocated instances so that there should not be any memory overlapping.  Is my assumption correct?  Could anyone help me solving this problem?
Revision 9:58 AM 7/7/2005
When I launch from MallocDebug, my code works perfectly!!!  I can open as many files as I want without having any problem.  Why is that?

2.  When I set the Project option to "Release" build, it gave me an error that I have never seen when I "Debug" built.  It says the followings:
     Undefined symbols:
     .objc_class_name_CIImage
    _kCIFormatARGB8
    collect2: Id returned 1 exit status
Could anyone explain the reason for this error message, please?

Any input/help is much appreciated!!!

----

Well, google tells me that     _kCIFormatARGB8 is a CoreImage symbol. Are you doing anything with CoreImage? Also, have you tried cleaning the target and rebuilding?

----

Yes, I'm using CoreImage for its filter.  And yes, I tried cleaning the target and rebuilt but got the same message.  Is this because of XCode 2.1?

---- Have you added the core image (ie quartzcore) framework to your project? By default debug builds use zerolink, so symbols aren't resolved unless they are needed (and hence you never get link errors at compile time), whereas with a release build everything needs to be able to link.

----

THANK YOU!!! After adding QuartzCore.framework, it woks great and actually much faster!!!

Now, another problem I am having is still there.  Without launching from MallocDebug, the application cannot process more than one file (please refer to the original 1st question)...  What is causing this???

*This is most likely a memory management issue. Without seeing some code, it's impossible to say.*

----

OK.  I will start a new thread with this problem.  I will put my code there.  Thank you!!

