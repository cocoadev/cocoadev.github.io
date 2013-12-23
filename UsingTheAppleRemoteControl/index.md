---
layout: page
title: UsingTheAppleRemoteControl
---


Hi, 

I like General/FrontRow. A lot. I have the "necessary" iMac G5... but there's a few things I'd like to change. Writing my own version of General/FrontRow seems like a fairly reasonable task using the available frameworks (QT, GL, applescript, etc), and would allow me to implement whatever features I wanted. However a necessary component would be an interface to the apple remote control. Has anyone seen, or had a go at themselves, working out how to interface with the remote? I assume its similar to the iPod remote, so its probably not limited to iMac owners. 

I'm usually an OS General/PhD student and hardware hacker, so while I get the theory of what should go on, the practice of reverse engineering the mac side of things is lacking. 

My investigations so far involve using class-dump to look at General/BezelServices, which is a private framework involved in handling the remote with frontrow. I've also used the system profiler to ascertain that its a USB based device. I've also foudn the /private/etc/mach_init_per_user.d/General/RemoteUI.plist file which contains the path to the front row application. I assume this General/FrontRow get launched when you touch the remote. I also class_dump'd the frontrow application itself, but there wasn't too much interesting in there. 

I haven't got far, but would be keen to know if anyone else has looked into it, or have some tips as to where to start looking?

Cheers, 

Dave. 

----

Before you start looking for a hack, I would first see if the apple remote driver posts distributed notifications. Just create a generic Cocoa Application and in any subclass that wakes after launch register for all distributed notification (the     awakeFromNib method is as good a place as any).

    
- (void)awakeFromNib {
    General/[[NSDistributedNotificationCenter defaultCenter] addObserver:self
                                                  selector:@selector(allDistributedNotifications:)
                                                  name:nil
                                                  object:nil];
}

- (void)allDistributedNotifications:(General/NSNotification *)note {
    General/NSString *object = [note object];
    General/NSString *name = [note name];
    General/NSDictionary *userInfo = [note userInfo];
    General/NSLog(@"<%p>%s: object: %@ name: %@ userInfo: %@", self, __PRETTY_FUNCTION__, object, name, userInfo);
}

- (void)dealloc {
    General/[[NSDistributedNotificationCenter defaultCenter] removeObserver:self];
    [super dealloc];
}



This is a long shot, but the remote is only five buttons. 

--zootbobbalu

----

Thanks! Something happened, but its not going to be as easy that methinks... Results below:

This is what happened the first time I pushed a button:
    
[Session started at 2005-12-20 12:40:53 +1100.]
2005-12-20 12:40:55.484 Blerg[4203] Did it!
2005-12-20 12:40:58.881 Blerg[4203] <0x334770>-General/[DaveObject allDistributedNotifications:]: object: com.apple.rcd name: com.apple.rcd.started userInfo: (null)


I haven't been able to get it to spit this up again. But it sounds like something worth investigating. rcd == remote control device?

Update: If I reboot the machine, and re-run the program, and press something, then we get the same notification again.

This is what happens when you actually invoke General/FrontRow (e.g. push the menu button rather than the volume or other buttons). 

    
[Session started at 2005-12-20 12:43:30 +1100.]
2005-12-20 12:43:47.501 Blerg[4215] Did it!
2005-12-20 12:43:58.481 Blerg[4215] <0x334770>-General/[DaveObject allDistributedNotifications:]: object: (null) name: com.apple.dashboard.dismiss userInfo: (null)
2005-12-20 12:43:58.560 Blerg[4215] <0x334770>-General/[DaveObject allDistributedNotifications:]: object: com.ati.ati_gl_driver name: ATI GL Driver userInfo: {gldisplaymask = 1; pid = 4211; }
2005-12-20 12:43:58.569 Blerg[4215] <0x334770>-General/[DaveObject allDistributedNotifications:]: object: com.ati.ati_gl_driver name: ATI GL Driver userInfo: {gldisplaymask = 1; pid = 4211; }


Dave. 

----

Since I don't have an iMac G5 I was only guessing that somthing might get posted. It doesn't look like there is a named notification that provides button state. Is General/FrontRow an Application or is it a service? --zootbobbalu

Did you see this page yet?

http://rgbdream.com/?p=51

----

Don't forget to change the path in the General/RemoteUI.plist file to your application (when it's finished, of course).

----
A little more playing around:

Running kextstat, I noticed the following couple of lines:

    
   76    1 0x690000   0x5000     0x4000     com.apple.iokit.General/IOUSBHIDDriver (2.2.5) <29 19 11>
   77    0 0x695000   0x4000     0x3000     com.apple.driver.General/AppleIRController (24) <76 29 19 11>


I had a look at the .kext file which I found in /System/Library/Extensions/General/AppleIRController.kext. The relevant bit seems to be:

    
	<key>General/IOKitPersonalities</key>
	<dict>
		<key>General/AppleIRController</key>
		<dict>
			<key>General/CFBundleIdentifier</key>
			<string>com.apple.driver.General/AppleIRController</string>
			<key>General/HIDDefaultBehavior</key>
			<string>General/RemoteControl</string>
			<key>General/HIDRemoteControl</key>
			<true/>
			<key>General/IOClass</key>
			<string>General/AppleIRController</string>
			<key>General/IOProviderClass</key>
			<string>General/IOUSBInterface</string>
			<key>bConfigurationValue</key>
			<integer>1</integer>
			<key>bInterfaceNumber</key>
			<integer>0</integer>
			<key>idProduct</key>
			<integer>33344</integer>
			<key>idVendor</key>
			<integer>1452</integer>
		</dict>
	</dict>
	<key>General/OSBundleLibraries</key>
	<dict>
		<key>com.apple.iokit.General/IOHIDFamily</key>
		<string>1.3.5</string>
		<key>com.apple.iokit.General/IOUSBFamily</key>
		<string>1.8</string>
		<key>com.apple.iokit.General/IOUSBHIDDriver</key>
		<string>2.1.4</string>
	</dict>


Ok. Got it. After more looking around, its actually very easy (trust apple to make me feel silly! :-)

You just have to connect to the device using the HID Manager since its just an HID device. For those who are interested, check out:

http://developer.apple.com/documentation/General/DeviceDrivers/Conceptual/HID/index.html

I'll probably write a little library to make using this thing even easier. Using Apple's test code, and modifying it to recognise _all_ product General/IDs, etc, rather than just the keyboard I get the following when I press the left, right, up, down, play, menu:

    
Buffer = 7d 00 04 00 00 
Buffer = 7d 00 00 00 00 
Buffer = 7d 00 03 00 00 
Buffer = 7d 00 00 00 00 
Buffer = 7d 00 10 00 00 
Buffer = 7d 00 00 00 00 
Buffer = 7d 00 20 00 00 
Buffer = 7d 00 00 00 00 
Buffer = 7d 00 02 00 00 
Buffer = 7d 00 00 00 00 
Buffer = 7d 00 01 00 00 
Buffer = 7d 00 00 00 00 


Now I guess I've got to write some code to interface to this properly, and I'll be in business. 

Thanks all for your help!

----

Can you explain how you got that "Buffer = 7d ..." data?  Were you monitoring some queue?  Via General/HIDManager?

---- 

I can't answer that directly, but I've hacked about with the HID Example here:-

http://developer.apple.com/documentation/General/DeviceDrivers/Conceptual/HID/HID_Examples.dmg

to demonstrate how to interface with the remote. If you take the General/HIDExample project, and replace the main.c with the one from here:-

http://bohemiannight.co.uk/graf/main.c

I've altered it so that instead of searching for all USB HID devices, it only looks for "General/AppleIRController", and I replaced the magic numbers for the usageKey and usagePage Key with the ones relevant to the remote (found using the HID Explorer app http://developer.apple.com/samplecode/HID_Explorer/HID_Explorer.html). This only handles quick keypresses, I haven't figured out how to handle holding the keys (I suspect different usageKey and usagePageKey values are needed, but I've not had time to investigate further). 

----

Would it be possible to put up your code for download?

---- 

However, if I really want to use the Apple Remote im my program, I still need to face the fact, that Apple designed the remote with the up and down arrows being the same as the volume up and volume down arrows. Pretty much useless in your own program. Whenever you want to use the Apple Remote in your own program the system changes the volume and displays the bezel effect. So how do we get rid of this behaviour? I found out, that when you move the rcd.app from the General/CoreServices folder to some other path and you make a "killall rcd", the volume up and down is disabled. However if you want to reverse this, you will need to restart your computer. I also found out, that when rcd is killed and you hit a volume button on the Apple Remote rcd gets launched by launchd. So that would explain, why it is disabled then. However launchd somehow rembers that rcd was not present and does not try it again next time. This is why you need to restart your mac. Front Row has some way of telling the rcd app that it wants exclusive access to the Apple Remote. I could not find out how. It doesn't work via distributed notifications. Maybe someone else has more luck than me.

----

Perhaps it's done by disabling system hotkeys? When I look in my loginwindow log (via Console.app) I see the following lines when I enter/exit Front Row:

    
Feb 21 03:15:34  [841] "Front Row" (0x13c2f) set hot key operating mode to all but UA disabled
Feb 21 03:15:34  [841] Hot key operating mode is now all but UA disabled
Feb 21 03:15:35  [841] "Front Row" (0x13c2f) set hot key operating mode to normal
Feb 21 03:15:35  [841] Hot key operating mode is now normal


I don't remember the API for controlling this, but it does exist.

----

This is great work guys...I just stumbled upon this site after thinking about getting a new Intel Mini. General/FrontRow doesn't quite have all the features I want but General/MediaCentral does so I was trying to see if there was an app that could allow controlling Front Row as well as any 3rd party app with the Apple Remote. Hope you guys can figure it out....it looks like you are close!

More info on General/MediaCentral here:
http://www.equinux.com/us/products/mediacentral/index.html

----

I was playing around with the app from http://bohemiannight.co.uk/graf/main.c and found that if I changed the flags to the open() function in General/MyTestHIDDeviceInterface() to "ioReturnValue = (*hidDeviceInterface)->open(hidDeviceInterface, 1);" then the app will get exclusive access to the remote and General/FrontRow will not activate and the system volume will not change.  Once the app exits then normal functionality is restored.

----

Excellent find! Thanks for the contribution!

----

I can't wait to see how this works out - how cool it will be to incorporate the apple remote in our apps!!!  yay!!!

General/EcumeDesJours

----

Alright, so I've done a little more experimentation with my code to see what happens when two apps try to ask for exclusive access to the remote.  Well, it turns out that the first app will return success from the open call, while the second app will get an error response saying that the device was already opened for exclusive access--but it can still create the queue and receive receive events.

So, I've got code now that looks something like this (now using a constant from <hid/General/IOHIDKeys.h> for the open flag):

    
ioReturnValue = (*hidDeviceInterface)->open(hidDeviceInterface, kIOHIDOptionsTypeSeizeDevice);
if (ioReturnValue == kIOReturnExclusiveAccess) {
    printf("Another app has already got exclusive access, but we can still listen for events.");
}


On the other hand, it looks like if I start the first app in nonexclusive mode and then start the second in exclusive mode, the first app continues to get the events.  So there must be some event that needs to be captured to let an app know that someone else has asked for exclusive access.

The other thing to notice is that the up and down buttons will send a press message when the button is pushed and the depress when it is released so you can track how long the button has been held.  All the other buttons, however, will send both events in succesion only after a click.  If you hold the button for too long, no event will be sent.

----

I modified the test app to listen for all events and not just the few that the code above checked for and noticed a few interesting things.  First, the events for the volume up and down are wrong.  Also, pressing and holding the left or right buttons actually generates different events that must also be checked.  There are also some additional combinations.  Pressing and holding the play button sends a "sleep" event.  Pressing and holding the menu and right buttons together sends the "pair remote" device.  Pressing and holding the menu and play buttons together will change the wireless ID for the remote and then send the "pair remote" event.  The registry lists two other vendor-specific codes but I haven't found a way to generate them.  Finally, it is also possible to detect a press and hold of the menu button even though General/FrontRow doesn't respond to this.

I'll write up a test app to summarize all of these conclusions and post a link to it after I get some sleep.
----
dors bien!
----
Based on the information on this page I coded a simple Objective-C class to use the apple remote in own programs. You can download the complete source with an example from http://www.martinkahr.com/source-code 

----
Here's a simple app which uses the above class and some General/AppleScript magic to drive Keynote....
http://users.ox.ac.uk/~wadh1342/tarballs/General/KeynoteDriver.tgz
Source at http://users.ox.ac.uk/~wadh1342/tarballs/General/KeynoteDriver.tar.bz2 (yes, I need to come up with a better namespace ;-))

----
Does anyone know if the IR receiver will receive any other IR codes or is it limited to the buttons on the Apple remote?  It would be great if it could receive other IR codes so you could use a Universal Remote control with it to control other things.

Jay
----
Similarly, presumably General/IOKit gets notified of the ID of the remote (so that the OS can do pairing); where's that manifest itself?

----
Weird. I was wondering what the Kernel Extension was for if the IR Remote is just a standard USB HID class device. HID class devices shouldn't need a .kext. So, I disassembled the /System/Library/Extensions/General/AppleIRController.kext, which appears to be dedicated to making the remote appear as a HID class device to the system. So, perhaps the remote is on some other bus, or Apple chose not to make the remote hardware itself appear as a HID class device on the USB? I think the hardware is indeed attached internally on the USB, so maybe they chose a proprietary USB protcol for some reason. The Kernel Extension seems to translate this proprietary protocol into HID class stuff, which rcd.app communicates with through the General/IOKit.

Also, I don't actually have a Mac with an IR Remote (So I had to be creative in acquiring the .kext) & I'm wondering if anyone knows how rcd.app is launched. Is it launched as a General/LaunchAgent with launchd? Is it always running, or does it somehow get launched only when a button is pressed?

Jerry

----

I'm an 'aspiring' developer, just beginning to program, and I was wondering if someone could explain exactly what is going on in the Apple Remote Class & how to link a press on the Apple Remote to a button in an application. 

Silent Bob

----

Great work guys.... Have anyone tried iPod Universal Dock and the Apple remote to see if any events are received ?
It would be great for us with a non remote controled enabled Mac.

Flemming

----

I believe the it goes directly to the iPod, and not through the USB to your computer.


----

you may want to check out Proxie, from Griffin, it's a free beta, maybe in Ver.1.0 by now. can change features of any hid to control whatever. It works pretty well for the junior hacker like my self. can control General/EyeTV and frontrow from the same remote, I'm using the General/AirClick to control both on a pre-IR Mac.
also, it allows you to control different functions by how long you hold a key/button. 
http://www.griffintechnology.com/products/proxi/

----

I also would be very interested in expanding the #of IR commands recognized... sorta like in emacs, when you do "describe-key"  and it will tell you F36 (but you didn't know that the special key was F36).. then you take that info and construct a command to act on F36... so I'd like to use an existing remote I have (with many more keys, and admittedly less elegant than the Apple remote, but potentially more functional)... point it at the Mini, have the Mini report that it got IR "stream" XYZ, and then I can create Applescript to act on reception of IR command XYZ.  similar to the way multi-button mice have control panels to assign their "extra" keys to cause actions.

Mike

----

If you want to integrate support for the Apple Remote into your application, please consider making it Remote Buddy compatible using the BSD-licensed RB SDK. Remote Buddy is an effort to avoid end user problems with too many applications exclusively locking the remote and the user having to negotiate between them. You can find more information and the description of such a scenario and its implications for the user here: http://www.iospirit.com/remotebuddy/ (click on "Developers" on the left side for the SDK)

Felix


----

Mira ( http://twistedmelon.com ) is also a program allowing users to use the Apple Remote with any application on their machines. Soon after it was first started general information on the remote appeared here at Cocoadev. It doesn't require any type of SDK however to add suppot for new applications. Developers and users alike can create profiles using some of the many built-in actions and can easily also create much more sophisticated Multi Actions as easily as editing a standard Mac OS plist file (TM will provide the details for all the keys used in the plist file). mira also supports all the features present when not running any special remote software (Sleep & Pairing with the correct visual feedback). Also of note is coming support for USB Macs that don't have a built-in IR receiver (this won't be using the iPod doc obviously :)  

Bruno

----

Do any of these frameworks (General/RemoteBuddy, Mira) also support detection/reading of the Elgato General/EyeTV remote(s)?  I assume that is handled by the General/EyeTV box itself, which them sends something in over the FW port, but is there a kext somewhere that continually scans for that and then issues notifications when it finds something?

Three General/EyeTV-related files installed on my sytem are

    
EyeTVCinery450AudioBlock.kext
General/EyeTVCinergyXSAudioBlock.kext
General/EyeTVClassicDontSeize.kext


but none of these have any code in them (just the Info.plist and General/PkgInfo).  Anyone done any investigations?

Jim

----

Can the IR receiever accept any IR signals or only the Apple Remote?  

If other remotes are supported as well, can someone put up some code nessisary to display the raw data from the IR port?  Don't need anything fancy, just someway to differentiate buttons.  It'll be a life saver!

Chris

----

As well as Chris, I would like to know about using the IR receiver at a low level. I was thinking that it could be used to detect when there is a person (or other warm object) in front of the computer. It would be nice to have an 'IR Camera' app like the one for the General/WiiMote which shows what the built-in IR sensor sees.

Vincent 6/Jan/2007

----

Assuming we can get at it, another exciting thing that could be done wiht that datastream would be an app that learns IR codes and translates them into Pronto Code or something sinmilar for use in programming universal remote controls.  Every remote manufacturer seems to have a different proprietary expensive device you need to learn remote codes in, but they all seem to accept Pronto codes. Well, that would be extiting for me, anyway :)

Ben   22 Feb 2007

----

There's an open source Media Centre for Mac OS X which uses the Remote Control called iTheater. You can download the source and check out how they use the remote control.  http://www.itheaterproject.com/ The IR stuff is only for switch level input and doesn't seem to provide any analog data which Vincent might need to use. 

Hope that Helps!
DP 27 Feb 2007

----

There is a cocoa class that does just what you want. Includes source. You can find it at http://www.martinkahr.com/source-code/ . I've been using it for awhile.
Code is now on github: http://github.com/martinkahr/apple_remote_control

BCD 28 Feb 2007

----

I've been testing the package from http://www.martinkahr.com/source-code/ and it seems to work well on my Debug builds, however when I try to make a Release build, the linker complains...

 /usr/bin/ld: Undefined symbols:
 _IOCreatePlugInInterfaceForService
 _IOIteratorNext
 _IOObjectGetClass
 _IOObjectRelease
 _IOServiceGetMatchingServices
 _IOServiceMatching
 _kIOMasterPortDefault

WF 16-Mar-2007

----

@WF: When using Martin Kahr's wrapper class, you have to link against General/IOKit.Framework for your Release build. --JVR, 2007-04-07

----

So did anyone every find out if the built-in IR port can read raw IR data? I want to be able to use tv/vcr/stereo/palmpilot/etc. remotes through my built in Mac Book Pro's IR port!  -Richard 05/27/07 

----

Well, the built-in IR receiver is just a USB device, so if somebody were feeling adventurous they could log all USB data and see what happens when you hit it with other remotes. -bigtex 06/25/07

----

That's an interesting idea. How do I go about logging USB? Is there a tool in the Developer folder for it?

----

Please note that Apple changed the internal values used to map the remote's buttons on Leopard, so you need to update your apps!

----

I do not understand why 3rd-party remotes are possible to use with Apple TV http://support.apple.com/kb/HT3296?viewlocale=en_US  which I guess has the same IR Reciever than a Mac Mini and won't work on Mini with OSX. Maybe someone with Apple TV can publish it's IR Driver?? 

----

The Apple TV has a different receiver with different firmware.

----

There's now a new, modern Objective-C class for the Apple Remote that works under OS 10.4, 10.5 and 10.6. Documentation, Sample Code and Developer Guide are available at http://www.iospirit.com/developers/hidremote/
