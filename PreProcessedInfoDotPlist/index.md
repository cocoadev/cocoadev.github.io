---
layout: page
title: PreProcessedInfoDotPlist
---

    
http://devworld.apple.com/documentation/DeveloperTools/Conceptual/XcodeUserGuide21/Contents/Resources/en.lproj/05_02_bs_targets/chapter_30_section_6.html


This feature was added in XCode 2.1, and should allow developers to write out XML for an applications Info.plist file using C macros and #include statements. 

It's probably a slightly more efficient way to share the same data between your application's code and the plist file (without having to read the plist file at runtime). This feature has probably been implemented to silence the cries of graybeards who yearn for the good old days, when they could preprocess their .r files before they were compiled into the resource fork of their application.

