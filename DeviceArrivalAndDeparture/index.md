---
layout: page
title: DeviceArrivalAndDeparture
---

I have written an NSView subclass which displays streaming video from FireWire video cameras and would like to receive notifications when said plug-and-play devices arrive and depart. Everything I have read points to IOKit, but I would be more comfortable using QuickTime if such functionality is available (I realize that this is probably outside QuickTime's scope). Later on, I will need similar notifications for all devices accessible through the ImageCapture framework.

At any rate, the following URL seems to answer most of my question but I cannot figure out how to define the device description dictionary needed to call IOServiceAddMatchingNotification(). Are FireWire devices considered storage devices by IOKit? If not, how can all of this be accomplished?

http://developer.apple.com/documentation/DeviceDrivers/Conceptual/AccessingHardware/AH_Finding_Devices/chapter_4_section_2.html

Thanks for any pointers, EliotSimcoe

----

*A little discovery would be to watch for any device and log out the details. Plug in and unplug your device(s) to see what's noticed and how.*

But exactly *what* should I be monitoring? I don't know how to watch for any device. That is part of what I am asking.

----

There is a tool called "ICANotificationListener" included with the ImageCapture SDK that logs ICA messages.  I can't say for sure if your FireWire camera will show up there, but if it does, then that tool will show any notifications associated with it and the following code will capture them.  Also, check out page 29 in the "Image Capture Architecture.pdf" file included with the SDK.

I call [self SetupNotification]; in the init method of my ImageCapture app, and it registers the notifications for me.  You need to give it a C function to call back, but after that you can jump back into your Cocoa.

    

- (void)handleICANotification:(ICARegisterEventNotificationPB *)thePB {
	switch (thePB->notifyType) 
    { 
        case kICAEventDeviceAdded:              // a new device was added 
            [self HandleDeviceAdded: (thePB->object)]; 
            break; 
        case kICAEventDeviceRemoved:            // an existing device was removed 
            [self HandleDeviceRemoved: (thePB->object)]; 
            break; 
    } 
}

void NotificationCallback(ICAHeader* header)
{ 
    Controller* controller = (Controller*)header->refcon;
	
    if(controller)
        [controller handleICANotification: (ICARegisterEventNotificationPB *)header];
} 

- (OSErr)SetupNotification {
	OSErr                                         err = noErr;
	ICARegisterEventNotificationPB   pb;
	
    memset(&pb, 0, sizeof(ICARegisterEventNotificationPB)); 
    pb.header.refcon = (UInt32)self;	// save a ref to the Controller so we can call it back
    pb.object     = NULL; 
    pb.notifyType = nil; 
    pb.notifyProc = (ICACompletion)NotificationCallback;
    err = ICARegisterEventNotification(&pb, nil); 
	
    return err; 
}

