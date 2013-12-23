---
layout: page
title: FinddevPathForUSB
---

Describe FinddevPathForUSB here.


Hi,

I am currently trying to use the POSIX API to read a 3D tracker that uses USB.  I am using the following code to
find the /dev path of the device (most of it is from USBPrivateDataSample.c):

    
//
int main (int argc, const char *argv[])
{
    mach_port_t 		masterPort;
    CFMutableDictionaryRef 	matchingDict;
    CFRunLoopSourceRef		runLoopSource;
    CFNumberRef			numberRef;
    kern_return_t		kr;
    long			usbVendor = kMyVendorID;
    long			usbProduct = kMyProductID;
    sig_t			oldHandler;
    
    // pick up command line arguments
    if (argc > 1)
        usbVendor = atoi(argv[1]);
    if (argc > 2)
        usbProduct = atoi(argv[2]);

    // Set up a signal handler so we can clean up when we're interrupted from the command line
    // Otherwise we stay in our run loop forever.
    oldHandler = signal(SIGINT, SignalHandler);
    if (oldHandler == SIG_ERR)
        printf("Could not establish new signal handler");
        
    // First create a master_port for my task
    kr = IOMasterPort(MACH_PORT_NULL, &masterPort);
    if (kr || !masterPort)
    {
        printf("ERR: Couldn't create a master IOKit port(%08x)\n", kr);
        return -1;
    }

    printf("Looking for devices matching vendor ID=%ld and product ID=%ld\n", usbVendor, usbProduct);

    // Set up the matching criteria for the devices we're interested in. The matching criteria needs to follow
    // the same rules as kernel drivers: mainly it needs to follow the USB Common Class Specification, pp. 6-7.
    // See also <http://developer.apple.com/qa/qa2001/qa1076.html>.
    // One exception is that you can use the matching dictionary "as is", i.e. without adding any matching 
    // criteria to it and it will match every IOUSBDevice in the system. IOServiceAddMatchingNotification will 
    // consume this dictionary reference, so there is no need to release it later on.
    
    matchingDict = IOServiceMatching(kIOUSBDeviceClassName);	// Interested in instances of class
                                                                // IOUSBDevice and its subclasses
    if (!matchingDict)
    {
        printf("Can't create a USB matching dictionary\n");
        mach_port_deallocate(mach_task_self(), masterPort);
        return -1;
    }
    
	
    // We are interested in all USB Devices (as opposed to USB interfaces).  The Common Class Specification
    // tells us that we need to specify the idVendor, idProduct, and bcdDevice fields, or, if we're not interested
    // in particular bcdDevices, just the idVendor and idProduct.  Note that if we were trying to match an 
    // IOUSBInterface, we would need to set more values in the matching dictionary (e.g. idVendor, idProduct, 
    // bInterfaceNumber and bConfigurationValue.
    //    
    
    // Create a CFNumber for the idVendor and set the value in the dictionary
    numberRef = CFNumberCreate(kCFAllocatorDefault, kCFNumberSInt32Type, &usbVendor);
    CFDictionarySetValue(matchingDict, 
                         CFSTR(kUSBVendorID), 
                         numberRef);
    CFRelease(numberRef);
    
    //Create a CFNumber for the idProduct and set the value in the dictionary
    numberRef = CFNumberCreate(kCFAllocatorDefault, kCFNumberSInt32Type, &usbProduct);
    CFDictionarySetValue(matchingDict, 
                         CFSTR(kUSBProductID), 
                         numberRef);
    CFRelease(numberRef);
    numberRef = 0;

    // Create a notification port and add its run loop event source to our run loop
    // This is how async notifications get set up.
    
    gNotifyPort = IONotificationPortCreate(masterPort);
    runLoopSource = IONotificationPortGetRunLoopSource(gNotifyPort);
    
    gRunLoop = CFRunLoopGetCurrent();
    CFRunLoopAddSource(gRunLoop, runLoopSource, kCFRunLoopDefaultMode);
    
    // Now set up a notification to be called when a device is first matched by I/O Kit.
    kr = IOServiceAddMatchingNotification(gNotifyPort,			// notifyPort
                                          kIOFirstMatchNotification,	// notificationType
                                          matchingDict,			// matching
                                          DeviceAdded,			// callback
                                          NULL,				// refCon
                                          &gAddedIter			// notification
                                          );		
                                            
    // Iterate once to get already-present devices and arm the notification    
	//readData(gAddedIter);
	//getFilePath(NULL, gAddedIter);
	DeviceAdded(NULL, gAddedIter);	

    // Now done with the master_port
    mach_port_deallocate(mach_task_self(), masterPort);
    masterPort = 0;

    // Start the run loop. Now we'll receive notifications.
	
    printf("Starting run loop.\n");
    CFRunLoopRun();
        
    // We should never get here
    printf("Unexpectedly back from CFRunLoopRun()!\n");
    return 0;
}




void DeviceAdded(void *refCon, io_iterator_t iterator)
{
    kern_return_t		kr;
    io_service_t		usbDevice;
    IOCFPlugInInterface 	**plugInInterface=NULL;
    SInt32 			score;
    HRESULT 			res;
    
    while (usbDevice = IOIteratorNext(iterator))
    {
        io_name_t		deviceName;
		char *deviceFilePath;
        CFStringRef		deviceNameAsCFString;	
		CFTypeRef deviceNameAsCFString2;
        MyPrivateData		*privateDataRef = NULL;
        UInt32			locationID;

        printf("Device 0x%08x added.\n", usbDevice);
	
        // Add some app-specific information about this device.
        // Create a buffer to hold the data.
        
		//NEW CODE--------------------------------------------------
		
		deviceNameAsCFString2 = IORegistryEntryCreateCFProperty(usbDevice, CFSTR(kIOCalloutDeviceKey),
																		kCFAllocatorDefault,0);
		if (deviceNameAsCFString2)
        {
            Boolean result;
 
        // Convert the path from a CFString to a NULL-terminated C string 
        // for use with the POSIX open() call.
 
        result = CFStringGetCString(deviceNameAsCFString2,
                                        deviceFilePath,
										MAXPATHLEN, 
                                        kCFStringEncodingASCII);
            CFRelease(deviceNameAsCFString2);
 
            if (result)
            {
                printf("BSD path: %s", deviceFilePath);
            }
        }


However deviceNameAsCFString2 always returns as NULL anyone know why?  I am pretty sure I am iterating through the right dictionary since I can get the device name and everything else, but not
the /dev path.

----

Are you sure the device you're looking for has a driver that makes a /dev interface?  (Principally a bluetooth or USB/Serial adapter?)


----

I honestly don't know, I thought itd be using the default Mac one since it is detected in the IOregistry, how can I be sure if a /dev is created or not?
thanks in advance.

