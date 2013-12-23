---
layout: page
title: IOServiceAddMatchingNotification
---

I am trying to write a Cocoa app that works with a USB device.

I am using the Apple sample code as a guide but it is code for a FOUNDATION tool - NOT a Cocoa app.

Here are the steps that I am doing:

1) I call IONotificationPortCreate - to create a notification object
2) I call IONotificationPortGetRunLoopSource- to get the runloop source of the object from step 1
3) call CFRunLoopAddSource with CFRunLoopGetCurrent as a parameter - to add the source to my runloop
4) I create a matching dictionary that matches my USB device - I tested this function and it DEFINTELY matches my USB device
5) I call IOServiceAddMatchingNotification - it returns err_none

Then my app never gets ANY notifications of USB device removal or attachment.

I even tried adding in a call to CFRunLoopRun like the Apple sample code does but my app just hung.

Has anyone got IOServiceAddMatchingNotification working from a Cocoa app?

I have this working on my cocoa app, watching for serial ports:
    
    // related instance variables:
    IONotificationPortRef   nport;
    io_iterator_t           serialPortIterator;

- (void)startWatchingForSerialPorts {
    nport = IONotificationPortCreate(kIOMasterPortDefault);

    CFMutableDictionaryRef match = IOServiceMatching(kIOSerialBSDServiceValue);
    CFDictionarySetValue(match, CFSTR(kIOSerialBSDTypeKey), CFSTR(kIOSerialBSDAllTypes));
    
    CFRunLoopAddSource(CFRunLoopGetCurrent(), IONotificationPortGetRunLoopSource(nport), kCFRunLoopCommonModes);
    // NOTE IOServiceAddMatchingNotification uses the dictionary, so we pass a copy
    IOServiceAddMatchingNotification(nport, kIOPublishNotification, CFDictionaryCreateMutableCopy(NULL, 0, match), (IOServiceMatchingCallback)serial_port_added, self, &serialPortIterator);
    while (IOIteratorNext(serialPortIterator)) {}; // could call serial_port_added(self, serialPortIterator) to notify of existing serial ports
    IOServiceAddMatchingNotification(nport, kIOTerminatedNotification, match, (IOServiceMatchingCallback)serial_port_removed, self, &serialPortIterator);
    while (IOIteratorNext(serialPortIterator)) {};
    
}

- (void)stopWatchingForSerialPorts {
    CFRunLoopRemoveSource(CFRunLoopGetMain(), IONotificationPortGetRunLoopSource(nport), kCFRunLoopDefaultMode);
    IONotificationPortDestroy(nport);
    IOObjectRelease(serialPortIterator);
}

- (void)didAddSerialPort:(NSString*) deviceName {
    [serialPortsMenu addItemWithTitle: deviceName];
}

- (void)didRemoveSerialPort:(NSString*) deviceName {
    [serialPortsMenu removeItemWithTitle: deviceName];
}

void serial_port_added (id self, io_iterator_t iter) {
    io_registry_entry_t serialPort;
    while (serialPort = IOIteratorNext(iter)) {
        CFTypeRef   path;
        path = IORegistryEntryCreateCFProperty(serialPort, CFSTR(kIODialinDeviceKey), kCFAllocatorDefault, 0);
        [self didAddSerialPort:[(NSString*)path lastPathComponent]];
        CFRelease(path);
        IOObjectRelease(serialPort);
    }
}

void serial_port_removed (id self, io_iterator_t iter) {
    io_registry_entry_t serialPort;
    while (serialPort = IOIteratorNext(iter)) {
        CFTypeRef   path;
        path = IORegistryEntryCreateCFProperty(serialPort, CFSTR(kIODialinDeviceKey), kCFAllocatorDefault, 0);
        [self didRemoveSerialPort:[(NSString*)path lastPathComponent]];
        CFRelease(path);
        IOObjectRelease(serialPort);
    }
}

