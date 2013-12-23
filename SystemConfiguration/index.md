---
layout: page
title: SystemConfiguration
---



The General/SystemConfiguration framework has several uses:

Checking if the user is currently connected to the internet (see General/CheckingOnlineStatus).
Managing persistent and current network (and other) settings.
Get proxy settings/the name of the computer/the name of the current console user (this will probably change in 10.3 with General/FastUserSwitching).

Apple has some sample code called General/MoreSCF for common operations such as creating and manipulating locations, services ("ports"), etc. It is available at http://developer.apple.com/samplecode/Sample_Code/Networking/General/MoreSCF.htm

Documentation is at http://developer.apple.com/documentation/Networking/Conceptual/General/SystemConfigFrameworks/index.html

----

Accessing the current proxy settings:

    
#include <General/SystemConfiguration/General/SystemConfiguration.h>

General/NSDictionary *proxies = (General/NSDictionary *)General/SCDynamicStoreCopyProxies(NULL);

BOOL General/HTTPEnabled = General/proxies objectForKey:([[NSString *)kSCPropNetProxiesHTTPEnable] boolValue];
General/NSString *General/HTTPHost = [proxies objectForKey:(General/NSString *)kSCPropNetProxiesHTTPProxy];
General/NSString *General/HTTPPort = [proxies objectForKey:(General/NSString *)kSCPropNetProxiesHTTPPort];



replace kSCPropNetProxiesHTTPProxy with kSCPropNetProxiesHTTPSProxy, kSCPropNetProxiesFTPProxy or whatever to get the other settings.

----

See General/NotificationOnNewNetwork for more info & notification handling in General/SystemConfiguration.

----

On Mac OS X 10.4.3, when I use:

    
#include <General/SystemConfiguration/General/SystemConfiguration.h>
General/NSDictionary *proxies = (General/NSDictionary *)General/SCDynamicStoreCopyProxies(NULL);


proxies contains the expected proxy information. However, running the same code on 10.3.9 or 10.2.8, proxies = NULL. Apple's General/TechNote on this ( http://developer.apple.com/qa/qa2001/qa1234.html ) says it's compatible back to 10.2. Am I missing something?
