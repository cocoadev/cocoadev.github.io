---
layout: page
title: DetermineFrameworkVersion
---



Is there a way to determine the current version of a Framework? For example I'd like to check if WebKit is of a minimum version in my code.

I've looked at the NSApplication version info, but that only seems to indicate up to 10.2.3, and seems specific to NSApp class only.

----

Get an NSBundle pointing to the framework (using     +bundleWithIdentifier: is probably the best route) and then look at the CFBundleVersion in its info dictionary.

*Ah, yes! Works perfectly:*
    NSLog(@"WebKit v.%@",[[[NSBundle bundleWithIdentifier:@"com.apple.WebKit"] infoDictionary] valueForKey:@"CFBundleVersion"]);

