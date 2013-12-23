---
layout: page
title: DiscRecording
---




Docs:
http://developer.apple.com/documentation/Carbon/Reference/discrecording/CocoaDoc/index.html

Sample Code: 
*http://developer.apple.com/samplecode/Sample_Code/DiscRecording.htm *(link broken)*
*http://developer.apple.com/samplecode/EnhancedAudioBurn/
*http://developer.apple.com/samplecode/AudioBurn/
*http://developer.apple.com/samplecode/DataBurn/
*http://developer.apple.com/samplecode/ContentBurn/
*http://developer.apple.com/samplecode/DeviceListener/
*http://developer.apple.com/samplecode/EnhancedDataBurn/


Mailing List:
http://lists.apple.com/mailman/listinfo/discrecording

----
http://www.diggory.net/grazing/burn.jpg

Burn CDs and DVDs from Cocoa!

DiscRecording seems to be made up of two frameworks, DiscRecording and DiscRecordingUI. First, the objects in DiscRecording.framework.

* DRDevice represents a physical CD/DVD drive connected to the computer. The class has methods for discovering CD/DVD burners, finding out their properties, examining media, and even a few simple commands such as opening and closing the tray. Unfortunately drives which can't burn (such as ordinary CD-ROMs) are not accessible through DRDevice.

* DRBurn handles the process of burning a CD or DVD disc. The burn itself happens on a separate thread, but you can sign up for notifications on the burn object to be notified of its progress.

* DRErase handles the process of erasing a rewritable CD or DVD disc. Similarly to DRBurn, the erase happens on a separate thread and you sign up for notifications on the object to know when it's complete.

* DRTrack represents a track on the burned disc. When you burn, you provide one or more tracks as a "layout" that is then written to the disc. You can provide your own data by implementing the DRTrackDataProduction protocol, or use the     +[DRTrack trackForRootFolder:] method to create a track containing a filesystem.

* DRFile and DRFolder are used by the filesystem content engine to represent files and folders. You can build a hierarchy of them just the way you'd imagine, and the engine will automatically build a hybrid filesystem with the layout you specify.

* DRNotificationCenter is a notification center class much like NSNotificationCenter that is specially equipped to handle DiscRecording notifications.

Don't forget the UI framework! Apple has provided some pretty nice panels in the DiscRecordingUI framework to help make it easy to burn. Be sure to check out DRBurnSetupPanel and DRBurnProgressPanel, and also DREraseSetupPanel and DREraseProgressPanel.

