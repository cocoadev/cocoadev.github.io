---
layout: page
title: GettingLocalHostName
---



How do I get the local host name, 
not this:

mikesmachine.local

but this:

Mike's Machine

----

This used to be called "Rendezvous Name" but is now called, simply, "Computer Name". The following snippet comes from the page HowToGetHardwareAndNetworkInfo ...

    
+ (NSString *)computerName
{
        CFStringRef name;
        NSString *computerName;
        name=SCDynamicStoreCopyComputerName(NULL,NULL);
        computerName=[NSString stringWithString:(NSString *)name];
        CFRelease(name);
        return computerName;
}

----
Since CFString and NSString are TollFreeBridged, this could be simplified to:
    
+ (NSString *)computerName {
   return [(id)SCDynamicStoreCopyComputerName(NULL, NULL) autorelease];
}



----

Tnx:)

Same type of question:

How do i get the name of the startupDisk?

----

GettingTheRootVolumeName

Use google to search cocoadev.  Works well.  http://www.google.com/search?q=site%3Acocoadev.com+volume+name

(LaunchBar 4 users:  use search template http://www.google.com/search?q=site%3Acocoadev.com+* )

