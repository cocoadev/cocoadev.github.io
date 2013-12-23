---
layout: page
title: IBCrashingOnOpeningNib
---

Yesterday IB started crashing when I open the NIB of the project I'm currently working on. It opens all other NIBs just fine. Has anyone had any problems with IB crashing suddenly when you try to open a NIB?

----

Yes. I submitted a bug report to Apple with the nib file attached and it's a 'known issue'

----

From IB 2.4.2 (v364) release notes (the xcode 1.2 version)

Cocoa Connection/Binding corruption fixed

There was a problem in Interface Builder where in some cases connections or bindings were being written incorrectly in the nib.  If you have a nib that causes earlier versions of Interface Builder to crash when it is opened for editing or causes an application to crash when loaded at run time, please open the nib in this version of Interface Builder and it will be repaired.

