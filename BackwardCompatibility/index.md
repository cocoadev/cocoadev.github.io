---
layout: page
title: BackwardCompatibility
---

This page is for information on maintaining backward compatibility with previous Mac OS versions.

Typical reasons for applications failing to launch on an older operating system are of two types. 

1) Changes in the Cocoa API which you may be calling a newer version or an old version incorrectly. Check OSXThroughTheAges for more information. You can work around these issues using this:
    
if (!(floor(NSAppKitVersionNumber) <= NSAppKitVersionNumber10_2)) { //Some code }


to ensure that it will only be run on Panther. This can be used with:

*NSAppKitVersionNumber10_1
*NSAppKitVersionNumber10_2
*NSAppKitVersionNumber10_2_3


Your other method is to see if respondsToSelector (it's in NSObject so call it on anything) returns true or false.

2) The other major problem can be NIB's being out of date. Apple has made it real difficult for all of us by revving the NIB version every time. Compatibility seems to have been improved in Interface Builder this time around. When adding new interface elements (NSSearchField) or selecting new options for other elements (NSTableView � setUsesAlternatingRowBackroundColors:) then it will ask what type of compatibility to keep. 

Of course, the argument about who is using what OS is going to take forever to work out, so I generally just keep my apps one step behind and if possible, two steps behind. Jaguar is quite full featured so it hasn't been too painful, whereas a lot of functions were added in the 10.1->10.2 jump making it extremely difficult to keep basic application functions working correctly. Of course, YMMV.

Hope this helps the original poster. Feel free to correct me, I'm sure i don't know everything. -- MatPeterson

