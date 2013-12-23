---
layout: page
title: CFHost
---

CFHost allows you to asynchronously resolve a name or address without the main thread blocking. CFNetwork will spawn the thread for you in the background and fire a notification back to the main thread once it acquires the information.


    

-(void)connectTo: (NSString *)address {
    BOOL success = NO;
    
    CFHostClientContext hostContext;
    
    hostContext.version = 0;
    hostContext.info = self;
    hostContext.retain = nil;
    hostContext.release = nil;
    hostContext.copyDescription = nil;
    
    CFStreamError error;
    
    CFHostRef host = CFHostCreateWithName(kCFAllocatorDefault, (CFStringRef)address);
    success = CFHostSetClient(host, _network_host_callback, hostContext);
    CFHostScheduleWithRunLoop(host, CFRunLoopGetCurrent(), kCFRunLoopDefaultMode);
    
    success = CFHostStartInfoResolution(host, kCFHostAddresses, &error);
}


