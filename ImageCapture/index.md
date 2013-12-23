---
layout: page
title: ImageCapture
---



Image Capture Architecture (ICA) provides system level support for image input devices, such as cameras and scanners. It is used by the Image Capture application and iPhoto. Developers can create their modules for cameras and scanners to use this interface.

At first glance there appears to be no documentation for this framework. Iin fact there is an SDK which you can get from http://developer.apple.com/sdk/ (along with oodles of sample code and useful tester applications).  Note that ICA is not a premier or well loved API, and the documentation is not updated for every major release, and the documentation can sometime be slightly behind the times.

This is the place to look if you're looking for something like a TWAIN or a direct ImageCapture interface to your scanner.

----
Has anyone any information on progressively collecting data one scanline at a time? Short of doing a custom properly, I do not see a way to properly do this under ImageCapture


----
Hey folks,  We have an ADF we are working on, and we did not see a standard property for setting scan surfaces.  There is a ICAP_DUPLEX (on/off) but nothing that handles TWADF_SIMPLEX_FRONT (1) /TWADF_SIMPLEX_REAR (2)  /TWADF_DUPLEX (3).  To handle this, I have added the following custom properties to our device. Feel free to use this same convention, or to suggest a better property or system if you know of one.   In the tradition of DRY, when using this property we do NOT use the property ICAP_DUPLEX (however, we silently accept it in cases where we can).
    
// Property for checking what surfaces a device has. Value is one of the 3 TWADF values above
 "CAP_NEAT_SCAN_SURFACES_AVAILABLE" =             {
                type = "TWON_ONEVALUE";
                value = 3;
            };

// Property for checking what surfaces are enabled. Value is one of the 3 TWADF values above
 "CAP_NEAT_SCAN_SURFACES_ENABLED" =             {
                type = "TWON_ONEVALUE";
                value = 3;
            };

