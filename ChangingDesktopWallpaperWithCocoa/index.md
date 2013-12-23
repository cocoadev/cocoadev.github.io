---
layout: page
title: ChangingDesktopWallpaperWithCocoa
---



Is it a large undertaking to write a Cocoa app that will change out the current desktop wallpaper to a specified image?  Is accessing a system setting like this quite a challenge?  Giving thought to an app that will change desktop wallpaper for multiple monitor folks - keeping a pair (or set of 3, 4, etc.) of images tied so that a scheduled change can take place in pairs (or sets of 3, 4, etc.).  OS X's built in wallpaper changer does not allow tied images for dual or more screen folks.  

Thanks.

blakespot

----
Have a look at: http://developer.apple.com/samplecode/DeskPictAppDockMenu/DeskPictAppDockMenu.html

I'm sure this could be easily modified to include multiple displays.

JacobHazelgrove

----
this code is old and oudated and i can't find any new information, it doesnt seem to work half of the time, and some of the comments in the code say there should be new api for changing the desktop.. any help?

----
So far as I know, the only way is still to send an AppleEvent to the Finder.

----

Here is the AppleEvent building part from the DeskPictAppDockMenu sample code:

AEBuildAppleEvent(kAECoreSuite,kAESetData,typeApplSignature,&sig,sizeof(OSType),
                                  kAutoGenerateReturnID,kAnyTransactionID,&tAppleEvent,&tAEBuildError,
                                  "'----':'obj '{want:type(prop),form:prop,seld:type('dpic'),from:'null'()},data:(@)",&tAEDesc);

The most important part is the formatting string wich basically describe the apple event itself:  "'----':'obj '{want:type(prop),form:prop,seld:type('dpic'),from:'null'()},data:(@)"
And the class of the event: kAESetData

But where on earth does it says that it will set the data (picture) to the *desktop* ?? I can't find any reference on the subject via google !
I need to be able to set the picture to any connected desktop (multiple screens) but I have no idea how to modify the formatting string to include mutliple displays.

Please Help !

----
Take a look at the code from VirtueDesktops: http://trac.virtuedesktops dot info/browser/trunk/Source/Virtue/Controllers/VTDesktopBackgroundHelper.m#L202 there are a couple of ways to do it, none of them quick. 

(btw: dot info domains are blacklisted here? guh...)

-- TonyArnold

----

You can do it though the preferences system.

defaults write com.apple.Desktop Background '{ 69667584 = { Change = Never; ChangePath = "/Library/Desktop Pictures"; CollectionString = "Apple Images"; ImageFileAlias = <00000000 00ac0003 00000000 bf4b373c 0000482b 00000000 0000ec26 0000ec30 0000be64 1f0f0000 00000920 fffe0000 00000000 0000ffff ffff0001 00080000 ec260000 0c04000e 001c000d 00410071 00750061 00200042 006c0075 0065002e 006a0070 0067000f 000e0006 004d0061 00630020 00480044 00120026 4c696272 6172792f 4465736b 746f7020 50696374 75726573 2f417175 6120426c 75652e6a 70670013 00012f00 ffff0000 >; ImageFilePath = "/Library/Desktop Pictures/Aqua Graphite.jpg"; Placement = Crop; TimerPopUpTag = 6; }; default = { Change = Never; ChangePath = "/Library/Desktop Pictures"; CollectionString = "Apple Images"; ImageFileAlias = <00000000 00ac0003 00000000 bf4b373c 0000482b 00000000 0000ec26 0000ec30 0000be64 1f0f0000 00000920 fffe0000 00000000 0000ffff ffff0001 00080000 ec260000 0c04000e 001c000d 00410071 00750061 00200042 006c0075 0065002e 006a0070 0067000f 000e0006 004d0061 00630020 00480044 00120026 4c696272 6172792f 4465736b 746f7020 50696374 75726573 2f417175 6120426c 75652e6a 70670013 00012f00 ffff0000 >; ImageFilePath = "/Library/Desktop Pictures/Aqua Graphite.jpg"; Placement = Crop; TimerPopUpTag = 6; }; }'

But you have to log out and log back in for the change to take place.

--CristianDraghici

----

I've been looking through the symbols in the Desktop system preference, and I found some methods that I can't seem to find if they are defined in the plugin or somewhere else. They are DesktopPictureSetDisplay, DesktopPictureSetValue, DesktopPictureCopyDisplay, DesktopPictureCopyValue. I also found these functions used in Finder too, but still can't locate where they originated from!


----

Those symbols come from HiServices which is loaded by ApplicationServices which in turn is loaded by DesktopServicesPriv (Finder is linked against it):

    
cristi:~ diciu$ nm /System/Library/Frameworks/ApplicationServices.framework/Versions/A/Frameworks/HIServices.framework/Versions/A/HIServices | grep Desktop
[..]
9183df10 T _DesktopPictureCopyDisplay
9183e090 T _DesktopPictureCopyValue
9186ebc8 T _DesktopPictureSetDisplay
9186edac T _DesktopPictureSetValue
9186024c t __DCXShowDesktopEnabled
9187eed8 t __DSRevealWindowForShowDesktop
[..]


Unfortunately they don't seem to be exposed (i.e. available in header files) or documented anywhere.
I think it's better to use something clumsy (i.e. preferences_ rather then using private frameworks that are likely to break.

Hope this helps,
--CristianDraghici

