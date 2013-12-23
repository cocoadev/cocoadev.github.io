---
layout: page
title: SharkWontFindSource
---

I am trying to use Shark to profile a small application and an underlying framework which it is built on.

The profile works fine and I get data which looks reasonable but Shark doesn't find the C source so all I get is the disassembly which isn't very helpful, on its own.

I have tried turning off all optimization (which partially defeats the purpose of checking performance behaviour), generating all debug symbols and not stripping, turning on any profile options I could find, setting the source path in Shark to include several of the directories where I have my source, etc, but nothing seems to help me out.

I have used Shark several times before but with smaller projects and it usually could find the source if I left in the debug symbols (which makes sense, it needs file and line number data) but this problem has me stumped.

Does anyone have any insight as to how to force Shark to find sources or at least get it to tell me why it can't find them?

---- Are you saying the moving from 'small application" to "small application plus framework" is the likely cause of the problem? Which version of XCode and tools? I am fairly sure that I have used shark succcessfully with Camino which has frameworks.

