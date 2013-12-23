---
layout: page
title: SetADateForAnAppToExpire
---



**Using NSDate to implement a trial period for a demo**


    	NSDate * today = [NSDate date];
	
	// pick a target date
	
	NSDate * targetDate = [NSDate dateWithString:@"2004-012-13 19:29:54 -0400"];
	
	if (!today laterDate:targetDate] isEqualToDate:targetDate]){
		[[NSRunAlertPanel(@"this beta has expired", @"please download a new one", nil, nil, nil);
		NSLog(@"expired");
		[[NSApplication sharedApplication] terminate:self];
	}


You could put it in your -init method.

----

Be aware that someone could hack your app's executable to expire on any date they like if you use this method (@"" constant strings are stored as plain text in the executable.) Creating the date as a c string or using     timeIntervalSinceReferenceDate would solve this.

To use     timeIntervalSinceReferenceDate:

    	NSDate * today = [NSDate date];
	
	NSTimeInterval expiry = *//(the number of seconds from Midnight, January 1st, 2001 GMT until you want your app to expire)*;
	if ([today timeIntervalSinceReferenceDate] > expiry){
		NSRunAlertPanel(@"this beta has expired", @"please download a new one", nil, nil, nil);
		NSLog(@"expired");
		[[NSApplication sharedApplication] terminate:self];
	}



----
How can I know how many seconds it is from that date? Any example?
-eb

----

Some wise individuals counsel that you should either simply have your application expire with an absolute date, regardless of when launched, as the scheme above assists, instead of using a relative date, or else strictly implement a licensing technology, without an expiry date for running without a license. **Be sure to tell testers that is what will happen.**  The aim is to annoy potential customers as little as possible, and yet give them a reasonable chance to evaluate the merits of your app. Note that the discussion below points out the serious pitfalls even of implementing an absolute expiry scheme. This is not the sort of thing you want to do unless your development is at a very stable stage, and not requiring frequent updates or public assistance with debugging.

----

**Discussion: Using an expiry date in conjunction with a licensing scheme**

I want my app to run for a certain amount of days, and then after that time period, prevent the user from using the app until they register it. I already have the code running for saving a date to a file, and then checking for that file and comparing it to the current date. But I feel that the method I use to save the date is insecure, and the only way I can think of making it more secure is to hide the file in a more unknown directory.  But if the user ever finds out the path to this file, they could easily reset the trial period just by removing the file.

Is there a better way to go about saving the trial period's starting date? And/or hiding it?  --Kevin

----

IMO, time spent on this is time wasted. Just save the date to your app's preferences and leave it at that. If someone *really* wants to, they're going to find a way to reset your trial period. Period. In particular, **don't** try to 'hide' your date file - it's just an annoyance, both to people trying to reset your trial period (and it won't stop them from doing it), and to legitimate users (who may have only run your app once) who'll wonder what $#@# application wrote this damn file.

If you try to make it 'more secure' here are some issues you'll have to deal with:

What if the user runs your app once, then a month or so later downloads a new version (or even re-downloads the same version). Will your app refuse to open? When people email you and ask to have their trial period reset for this very reason will you do it? how?

If you try checking the network for the date, how will you deal with the inevitable VersionTracker comments to the effect *"This is spyware!! It connects to the net everytime it starts up!"* What if the system your app is running on isn't connected to the internet?

What if the user simply resets their system clock to a later date? What about daylight savings time? What if their clock battery is dead?

Sorry if this sounds contentious - I've run into all these issues at various times, with various applications, and I've never once paid for any of them, and it always gave a bad impression of the company/individual responsible. Concentrate on making your application better so people will **want** to support and pay for it, and not assuming all your users are out to rip you off.

DetectFirstRun contains a discussion about using the defaults database to store time information related to this issue

----

If you are going to put a time limit on the app be sure and make it very clear so there are no surprises.

But in your support, might I recommend providing a "demo license" along with the download. Make sure the app REQUIRES some license to run; then give them one that expires. Omni Group does this; although I think their demo time of 1 day is a bit short. But you get the idea.

----

How about this scheme: when the application starts, it checks the presence of ~/Library/Preferences/com.myCompany.myApplication,


*if it does not exist, write today + 30 days into NSUserDefaults and also place a checksum on the date
*if it exists, read the expiration date from NSUserDefaults, if there is none, the checksum is incorrect, or it's more than 30 days into the future, write today + 1 to the user defaults (and tell the user that he has been a bad little boy!)


So for the user to bypass the protection, he'd have to delete his user defaults, which I think most users would dislike, if they are fond of the application (unless they are smart enough to move it to /Library/Preferences, but they'd still have to delete the user-local copy each time they restart the application -- and you could check all the Library folders).

--AllanOdgaard

----

So simply deleting the defaults key for the date every day (trivial to automate) would enable infinite free usage of the app?

----

Indeed -- but for how many users is it trivial to have a script run every day which removes keys from an xml file? Furthermore, the user would get the "You've been a bad boy"-requester each day.

Do you think there is a better alternative which doesn't degrade the experience for honest users?

And if you really do think users would have a script remove the keys, then you could refuse to start when the key is missing but the defaults file exist. Though I think getting a polite requester telling that the user did something dishonest would motivate more sells than "being evil" --  after all, the author showed some goodwill, and that might spread! :)

*No, this is a bad idea. Honest users who somehow managed to mess up the file will be insulted, and determined pirates who wanted to keep using the software without paying will just be spurred on by the challenge. Never accuse your user of anything, whether or not he has actually done it. Unless you live inside the Disney version of Pinocchio, being told of their lies isn't going to shame them into going legit. Using expiring licenses is probably the tightest you're going to get your secruity without shooting yourself in the foot.*

**What are you referring to when saying �this is a bad idea�? only bringing up the requester, or the entire scheme? As a user I generelly do not bother trying out software if it means that I need to enter my email address to get sent an expiring license. OmniGroup may get away with it due to their great reputation, but I'm not sure it'll benefit Joe Random Coder!?!**

----

Note added, January 2006: There is copious further discussion of software licensing security schemes in SoftwareSerializationPiracyDiscussion

