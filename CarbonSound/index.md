---
layout: page
title: CarbonSound
---



CarbonSound is an old sound API, largely superseded by CoreAudio and QuickTime.

Apparently QuickTime 7.0 removed the "CarbonSound/CarbonSound.h" header causing compilation errors when "Carbon/Carbon.h" is included.  Defining "__CARBONSOUND__" before including Carbon.h stops it from trying to include the missing header file:

    
  #define __CARBONSOUND__
  #include <Carbon/Carbon.h>


Another solution to this is to manually run Software Update again after installing QuickTime 7.0 and install the "QuickTime SDK" which is then available (it wasn't shown when the QuickTime 7.0 update was offered).

Ironically, the SDK installer prods Software Update to "check for new software" when it completes but I guess the QuickTime one must not (or this situation would not have arisen).

To summarize, you'll need to install the QuickTime SDK via software update if you update to QuickTime 7.0 on any machine you intend to build code on.

