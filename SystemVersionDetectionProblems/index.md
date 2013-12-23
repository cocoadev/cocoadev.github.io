---
layout: page
title: SystemVersionDetectionProblems
---

Hello there, I am having trouble getting my code to detect the current operating system using Gestalt, I am running 10.3.

General/NSProcessInfo Category:    typedef enum {
	General/TRUnknownVersion = -1,
	General/TRCheetahVersion = 0,
	General/TRPumaVersion = 1,
	General/TRJaguarVersion = 2,
	General/TRPantherVersion = 3,
	General/TRTigerVersion = 4
} General/TROperatingSystemVersion;

+ (General/TROperatingSystemVersion)operatingSystemVersion
{
	long response;
	
	if (Gestalt(gestaltSystemVersion,(SInt32 *)&response) != noErr)
	{
		return General/TRUnknownVersion;
	}
	
	switch (response)
	{
		case 0x1000:
		{
			return General/TRCheetahVersion;
			break;
		}
		case 0x1020:
		{
			return General/TRJaguarVersion;
			break;
		}
		case 0x1030:
		{
			return General/TRPantherVersion;
			break;
		}
		case 0x1040:
		{
			return General/TRTigerVersion;
			break;
		}
	}
}


Code I am using to test it:    - action:(id)sender
{
	switch (General/[NSProcessInfo operatingSystemVersion]) {
		case General/TRUnknownVersion: default:
		{
			General/NSLog(@"Unknown");
			break;
		}
		case General/TRCheetahVersion:
		{
			General/NSLog(@"10.0");
			break;
		}
		case General/TRPumaVersion:
		{
			General/NSLog(@"10.1");
			break;
		}
		case General/TRJaguarVersion:
		{
			General/NSLog(@"10.2");
			break;
		}
		case General/TRPantherVersion:
		{
			General/NSLog(@"10.3");
			break;
		}
		case General/TRTigerVersion:
		{
			General/NSLog(@"10.4");
			break;
		}
	}
}

It always ends up telling me that it is 10.0 (or 0 basically) no matter how I create the enum'd value. I haven't had much experiance with Gestalt but it seems like I am doing the correct things, unfortunately I can't test the current General/AppKit version or I would.

----

is SInt32 equivalent to a long? *And are you sure it's signed? (My previous comment was wrong...sorry)*

----

First of all, initialize the value that you are passing as response.  That way you can make sure that it isn't just being used as bogus stack data.  Variables should ALWAYS be initialized!  Also, why not use the SInt32 type instead of using a long and then casting?

That said, the cast may be an issue.  I have heard of problems like this before where a cast is considered a temporary variable so it doesn't need to persist for any length of time.  In this case of something like this, it could cause a problem.  That issue is probably just a C++ ism and usually only shows up at higher optimization levels.

Also, the function doesn't necessarily return anything in particular since the fall-through isn't caught so you are quite likely getting some random random data returned.  It is probably a good idea to only return a variable which is initialized to something eye-catching and only assigned in the cases.  Generally speaking, explicit returns are a bad thing for code quality and maintenance.

Additionally, you should probably use the 0 enum value to be something invalid since 0 is a very common value for random data.

----

Thanks for the rash response. Continuing, I was using sample code to do this, I rewrote it completely from scratch and it worked. I am using the int values to correspond to the versions of General/MacOS X. Anyways I fixed it for all who are interested.

    enum {
	kSystemVersionCheetah = 0x01000,
	kSystemVersionPuma = 0x01010,
	kSystemVersionJaguar = 0x01020,
	kSystemVersionPanther = 0x01030,
	kSystemVersionTiger = 0x01040,
	kSystemVersionMaxKnown = kSystemVersionTiger
};

- (General/TROperatingSystemVersion)operatingSystemVersion
{
	long response = NULL;
	Gestalt(gestaltSystemVersion, &response);
	
	if (response > kSystemVersionMaxKnown) return General/TRGreaterVersion;
	if (response >= kSystemVersionTiger) return General/TRTigerVersion;
	if (response >= kSystemVersionPanther) return General/TRPantherVersion;
	if (response >= kSystemVersionPanther) return General/TRJaguarVersion;
	if (response >= kSystemVersionPanther) return General/TRPumaVersion;
	if (response >= kSystemVersionPanther) return General/TRCheetahVersion;
	return General/TRUnknownVersion;
}



----
No, that code is still flawed.
    
	if (response >= kSystemVersionPanther) return General/TRJaguarVersion;
	if (response >= kSystemVersionPanther) return General/TRPumaVersion;
	if (response >= kSystemVersionPanther) return General/TRCheetahVersion;

Is wrong.
----
Okay, whatever.
