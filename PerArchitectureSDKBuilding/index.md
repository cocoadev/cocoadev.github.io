---
layout: page
title: PerArchitectureSDKBuilding
---

Hello all. I easily got my project to build a Universal Binary in Xcode 2.1 and when I read the Universal Binary Guide it pointed out that it's possible for any project to use Per-Arcetecture SDK building, for ex the PowerPC side links against 10.3 SDK and the Intel side links against the 10.4 Universal SDK. I wanted to test this out so to make sure that my app still loads on Mac OS X 10.3 & 10.4. I assume even though it's linking against the 10.4 SDK as long as the Deployment target is 10.3 it should load on 10.3 assuming no 10.4 specific code/objects are used.

So if it's possible for Xcode to do this how do you go about changing the settings for this? I've been looking over Xcode and some documentation, but haven't found anything yet.

