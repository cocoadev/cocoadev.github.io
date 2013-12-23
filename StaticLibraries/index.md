---
layout: page
title: StaticLibraries
---



This topic is for issues related to static libraries. 

Static libraries will not be linked if General/ZeroLink is enabled. If you get the following error:

    
General/ZeroLink: unknown symbol


you are probably building your target with General/ZeroLink enabled. See General/ZeroLink for details on how to turn this feature off.
