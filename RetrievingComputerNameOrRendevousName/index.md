---
layout: page
title: RetrievingComputerNameOrRendevousName
---

How can I retrieve via Cocoa the Computer or Rendevous name as set in Preferences->Sharing?

What framework has access to this sort of information?

----

NetInfo. Have a look at Apple's site.

----

I just had to tackle this one this evening. This relies on the ConfigurationFramework. It is quick and it is dirty

    
// Get a handle on the system config
SCDynamicStoreRef dynRef=SCDynamicStoreCreate(kCFAllocatorSystemDefault, (CFStringRef)@"App name", nil, nil);
            
// Grab the entry out of the config
NSDictionary *computerNameEntry = (NSDictionary *)SCDynamicStoreCopyValue(dynRef,(CFStringRef)@"Setup:/System");

NSString *computerName = [computerNameEntry objectForKey:@"ComputerName"];


An interesting thing to note is that there doesn't appear to be a way to get the RendezVous name like this. Further, in my app it's the ComputerName entry that's used when a service is advertised not the RendezVous name listed under SystemPreferences. I'd love to hear more about this.

-- JamesMoore (10/31/02)

----

You can also get the IP address and local computer name like this:

    
NSString *theIP = [[NSHost currentHost] address];
NSString *theName = [[NSHost currentHost] name];


In my case, this returns 192.168.1.3 (my local IP) and TiBook.local. (my local name)

-- JoeZobkiw

----
There's a difference between these two code snippets. The first retrieves the info from the system configuration database the second I believe will retrieve from netinfo. The first allows you to get the value for the computer name, the second allows you to get the hostnames for the machine.

-- JamesMoore (11/01/02)

----
Apple has a tech Q&A about this: http://developer.apple.com/qa/qa2001/qa1228.html

On a system that supports it (Panther for certain and reportedly Jaguar), pass the service name as an empty string and Rendezvous/Bonjour will retrieve the computer name itself and handle naming conflicts for you. 

Unfortunately, this functionality is broken in Tiger. On Tiger systems, the name will not be automatically generated, instead remaining an empty string. This will cause publication of the service to fail with a bad parameter error (-65540).

-- ColinMattson (5/25/05)

----

About IP addresses: You sould keep in mind, that a Mac may have several IP addresses (which can change), through it can be connected to different nets/subnets and RendezVous/Bonjour "areas"

----

This seems to work great and can be found soon as part of some open source development of ours:

    
- (NSString *)localBonjourHostName {
    SCDynamicStoreRef dynRef = SCDynamicStoreCreate(kCFAllocatorSystemDefault, (CFStringRef)@"TCMPortMapper", NULL, NULL); 
    NSString *hostname = [(NSString *)SCDynamicStoreCopyLocalHostName(dynRef) autorelease];
    CFRelease(dynRef);
    return [hostname stringByAppendingString:@".local"];
}


-- DominikWagner (3/01/08)

----

Per the QA above you should be using the ComputerName as Bonjour services are suggested to be registered using the ComputerName, not the LocalHostName
Thus:
    
CFStringEncoding theEncoding;
SCDynamicStoreRef dynRef = SCDynamicStoreCreate(kCFAllocatorSystemDefault, CFSTR("MyName"), NULL, NULL); 
CFStringRef computerName = SCDynamicStoreCopyComputerName(dynRef, &theEncoding);

-- (5/23/08)

