---
layout: page
title: PreferencesByHost
---

I am writing a program that creates ScreenSaver modules.  I am using the .slideSaver bundle to create the actual ScreenSaver.  One option I am trying to implement is the ability to configure the standard apple .slideSaver options in my program.  

I have located the preferences file created by System Preferences>Screen Effects.  The preference files are stored in $(HOME)/Library/Preferences/ByHost.  The problem is that all these files have a an odd entry in their names.  For example one file I have is com.apple.screensaver.Beach.0050e4eea57e.plist I have no idea what this (0050e4eea57e) entry is or how to determine it.  

Thank you very much for looking reading this. ek.

----
For what I know, you should not need to know, NSUserDefaults will handle that for you. Just do preferences like in any other app, and said NS-class will handle all the byhost stuff for you. That said, I think it is the MAC-address in the name. -- EnglaBenny

----
That is the MAC address in the name, but that's effectively an implementation detail and you shouldn't rely on that - in order to set preferences in a specific preferences domain, you're looking for the CFPreferencesSetValue() call:

     CFPreferencesSetValue(key, value, CFSTR("com.ek.screensaver"), kCFPreferencesCurrentUser, kCFPreferencesCurrentHost);

which sets the key-value pair in the com.ek.screensaver plist with the right info.

More info on this at:

     http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFPreferences/CFPreferences.html

or the API reference:

     http://developer.apple.com/documentation/CoreFoundation/Reference/CFPreferencesUtils/Reference/reference.html

It's important to note that while CFPreferencesSetValue() is the only way to set a by-host preference, you can retrieve the right thing transparently by using NSUserDefaults' -objectForKey: method. See the page on preferences domain search paths at the Programming Topics link (the first one).

- ChrisParker

----

It appears that with Mac OS X 10.5.2, Apple has changed the formula for generating ByHost preferences from the MAC address to, well, something else (looks like a CFUUID; see IDentifiers).

Examples:

Old:     com.apple.screensaver.AABBCCDDEEFF.plist (MAC addr)
New:     com.apple.screensaver.AAAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE.plist

Anyone know exactly what system values are being baked into this new ID?

--DanSandler

----

It's the IOPlatformUUID

If you just need to know what it is, run this on the command line:

    
ioreg -rd1 -c IOPlatformExpertDevice | grep -E '(UUID)'


Otherwise, here is some code that will fetch it for you:
Note that you will need to link to IOKit.framework and Foundation.framework for this to work.

    
- (NSString*)getUUIDString
{
	CFTypeRef	uuidAsCFString;
	NSString	*uuidString;
	
	io_service_t platformExpert = IOServiceGetMatchingService(kIOMasterPortDefault, IOServiceMatching("IOPlatformExpertDevice"));
	
	if (platformExpert) {
		uuidAsCFString = IORegistryEntryCreateCFProperty(platformExpert, CFSTR(kIOPlatformUUIDKey), kCFAllocatorDefault, 0);
		IOObjectRelease(platformExpert);
		uuidString = [[NSString alloc] initWithFormat:@"%@",uuidAsCFString];
	}
	
	return uuidString;
}


--Mathew Eis

