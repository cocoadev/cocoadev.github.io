---
layout: page
title: SerialBoxDetect
---

Hello, I wrote a small test application to see if it is feasible to detect if a user is running SerialSeeker (pirated serial numbers)...I may not end up using it anywhere, but I decided that it may benefit someone.  http://www.yetanothersite.com/joel/SerialBoxDetect.zip

The app looks for SerialSeeked when launched, and can detect if SerialSeeker is launched anytime when the app is running.

----

You'll probably end up getting the spotlight placed uncomfortably on your head for "policing" practices like that. What happens if something goes wrong and a misjudgment accidently accuses a user of piracy? What a fun PR event *that* would be. :-)

----

    
		if (app objectForKey:@"[[NSApplicationName"] isEqualToString:@"SerialSeeker"] || app objectForKey:@"[[NSApplicationBundleIdentifier"] isEqualToString:@"org.crackezz.serialseeker"]) {
			running = YES;
		}


Well, that's pretty solid code. It's very unlikely that legit apps have org.crackezz.serialseeker as their bundle ID! - EmanueleVulcano aka l0ne

----
I, as a software developer, frequently download and run applications such as SerialBox in order to search for codes to my applications that I can blacklist. Does that mean your software is going to treat me like a pirate?

Consider a user who runs SerialBox and perhaps even uses codes from it on occasion, but he paid for your app. Is your app going to treat him like a pirate?

This stuff always backfires far more than it helps. If you really want to maximize sales, I recommend doing the following, in order:


*Make a good app
*Make an app that people will want to buy
*Use a serial number scheme that is difficult or impossible to create a serial number generator for
*Blacklist the serial numbers that show up in the aforementioned apps


-- PrimeOperator

----
I agree with many of the points stated here (I am the original poster), and I opted to not use it.  'Twas just a test of if it could be done reliably, which apparently, it can.  The most I imagined doing with this code was showing a warning saying to not pirate software or something.

