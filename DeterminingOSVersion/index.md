---
layout: page
title: DeterminingOSVersion
---

----
**Abstract**

----
How to determine the version of the operating system your application is running on.



----
**Overview**

----
Unfortunately, over the years there have been a wide variety of solutions to determining the version of the OS the user is running, due to Apple's indecisiveness and/or inattentiveness, and Carbon phobia. In Mac OS 9, it was clear cut: Gestalt knows all. With Cocoa being the preferred development API on Mac OS X, Gestalt seems far too old school for use by a modern Cocoa programmer, and the search began.




----
**NSA<nowiki/>ppKitVersionNumber? No (or Sometimes).**

----
    NSAppKitVersionNumber is a constant defined in the AppKit framework, which, not surprisingly, defines "The version of the AppKit framework". On the surface it seems like this is a nice and simple way for a Cocoa programmer to determine the OS version, but it's not perfectly clear. First of all, NSApplication.h is sadly missing a ton of constants. As of the developer tools version 3.0 release, there are only the following constants:

    
#define NSAppKitVersionNumber10_0 577
#define NSAppKitVersionNumber10_1 620
#define NSAppKitVersionNumber10_2 663
#define NSAppKitVersionNumber10_2_3 663.6
#define NSAppKitVersionNumber10_3 743
#define NSAppKitVersionNumber10_3_2 743.14
#define NSAppKitVersionNumber10_3_3 743.2
#define NSAppKitVersionNumber10_3_5 743.24


Not exactly all-encompassing. Why the constants are hit and miss (mostly miss) is a mystery, but regardless of the missing constants, NSAppKitVersionNumber is only there to define the *AppKit* version number. Although AppKit certainly gets revisions in most updates, it's not guaranteed, and finding the constants can be rather annoying. NSAppKitVersionNumber is best used for deteriming the AppKit version number, not the OS version.

----
It's not all bad news though--in a Cocoa app, the framework version may be exactly what you need. More constants are available now (as of dev tools 3.1 at least), up through NSAppKitVersionNumber10_4. To determine whether you're on Leopard:
    
if (floor(NSAppKitVersionNumber) > NSAppKitVersionNumber10_4) {
// 10.5
}

If you want 10.4 minor release versions, through 10.4.11, you can use the Foundation flavor, NSFoundationVersionNumber, in NSObjCRuntime.h . See http://developer.apple.com/documentation/DeveloperTools/Conceptual/cross_development/chapter_5_section_1.html 

Also, at the Cocoa level, instancesRespondToSelector can be used to see if the desired method is available without needing to get the OS Version.
----


----
**S<nowiki/>ystemVersion.plist? No. Wait, yes.**

----
Although the official stance was to use Gestalt, even as far back as 2001, some developers were reading the plist file located at     /System/Library/CoreServices/S<nowiki/>ystemVersion.plist to get the version number. At some point (it seems in 10.4's initial release) a comment appeared in Gestalt.h:

    
    A better way to get version information on Mac OS X would be to read in the system
    version information from the file /System/Library/CoreServices/S<nowiki/>ystemVersion.plist.


The use of this file, although odd, seemed to be validated and recommended directly by Apple, so its use spread. Apple Developer Eric Schelegel cleared this matter up nicely in an email to the Carbon-Dev mailinglist in August 2007. [http://lists.apple.com/archives/carbon-dev/2007/Aug/msg00089.html]

    
That comment is incorrect and has been removed in Leopard. The best
way is just to use the B<nowiki/>ugFix/Minor/Major release version selectors.

-eric



And then further, according to this [http://stackoverflow.com/questions/11072804/mac-os-x-10-8-replacement-for-gestalt-for-testing-os-version-at-runtime StackOverflow question], Apple recommended at the 2012 WWDC to use this method for getting the system version, as crazy as that may be.  Here is some sample code:

    
 void GetSystemVersion( int &major, int &minor, int &bugfix )
 {
 	// sensible default
 	static int mMajor = 10;
 	static int mMinor = 8;
 	static int mBugfix = 0;
 
 	static dispatch_once_t onceToken;
 	dispatch_once(&onceToken, ^{
 		NSString* versionString = NSDictionary dictionaryWithContentsOfFile:@"/System/Library/CoreServices/SystemVersion.plist"] objectForKey:@"ProductVersion"];
 		NSArray* versions = [versionString componentsSeparatedByString:@"."];
 		check( versions.count >= 2 );
 		if ( versions.count >= 1 ) {
 			mMajor = [versions[0] integerValue];
 		}
 		if ( versions.count >= 2 ) {
 			mMinor = [versions[1] integerValue];
 		}
 		if ( versions.count >= 3 ) {
 			mBugfix = [versions[2] integerValue];
 		}
 	});
 
 	major = mMajor;
 	minor = mMinor;
 	bugfix = mBugfix;
 }




----
**Gestalt was the Right Way**

----
The truly correct way to determine the OS version is to use Gestalt. 

Or at least, it was.  Until 10.8 deprecated Gestalt, leaving no legitimate way to get the system version, and recommending using S<nowiki/>ystemVersion.plist.

Prior to Mac OS X 10.4, there was simply the Gestalt selector     gestaltSystemVersion which reported the entire system version as a 32-bit hex number, such as 0x1037 for Mac OS X 10.3.7. The problem with gestaltSystemVersion is that it's limited in the range of versions it can support. For example, 10.4.11 is actually reported as 0x1049. This limitation probably helped the spread of other methods to determine the OS version.

In Mac OS X 10.3 and later, however, three additional selectors are available:     gestaltSystemVersionMajor,     gestaltSystemVersionMinor, and     gestaltSystemVersionBugFix. These new selectors are what you should be using in your code. (These selectors are also defined in the 10.3.9 SDK, but they are not officially supported until 10.4.)

It should be noted that Gestalt is officially part of [[CoreServices, not Carbon, and is therefore 64-bit safe and future-proof.

**    gestaltSystemVersionMajor,     gestaltSystemVersionMinor, and     gestaltSystemVersionBugFix are all available in 10.3. I'm not sure why the above mentions they are only available in 10.4. I just confirmed by looking at the 10.3.9 SDK Gestalt.h file and the documentation**

----
**Cocoa Code for Gestalt**

----
Here is a bit of simple code to use Gestalt in an NSApplication category method, that is backwards and forwards compatible from 10.0 on. 

    
 #import <Cocoa/Cocoa.h>
 
 @interface NSApplication (SystemVersion)
 
 - (void)getSystemVersionMajor:(unsigned *)major
                         minor:(unsigned *)minor
                        bugFix:(unsigned *)bugFix;
 
 @end
 
 @implementation NSApplication (SystemVersion)
 
 - (void)getSystemVersionMajor:(unsigned *)major
                         minor:(unsigned *)minor
                        bugFix:(unsigned *)bugFix;
 {
     OSErr err;
     SInt32 systemVersion, versionMajor, versionMinor, versionBugFix;
     if ((err = Gestalt(gestaltSystemVersion, &systemVersion)) != noErr) goto fail;
     if (systemVersion < 0x1040)
     {
         if (major) *major = ((systemVersion & 0xF000) >> 12) * 10 +
             ((systemVersion & 0x0F00) >> 8);
         if (minor) *minor = (systemVersion & 0x00F0) >> 4;
         if (bugFix) *bugFix = (systemVersion & 0x000F);
     }
     else
     {
         if ((err = Gestalt(gestaltSystemVersionMajor, &versionMajor)) != noErr) goto fail;
         if ((err = Gestalt(gestaltSystemVersionMinor, &versionMinor)) != noErr) goto fail;
         if ((err = Gestalt(gestaltSystemVersionBugFix, &versionBugFix)) != noErr) goto fail;
         if (major) *major = versionMajor;
         if (minor) *minor = versionMinor;
         if (bugFix) *bugFix = versionBugFix;
     }
     
     return;
     
 fail:
     NSLog(@"Unable to obtain system version: %ld", (long)err);
     if (major) *major = 10;
     if (minor) *minor = 0;
     if (bugFix) *bugFix = 0;
 }
 
 @end
 
 int main(void)
 {
     NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
 
     unsigned major, minor, bugFix;
     [[NSApplication sharedApplication]
         getSystemVersionMajor:&major minor:&minor bugFix:&bugFix];
     NSLog(@"%u.%u.%u", major, minor, bugFix);
     
     [pool release];
     return EXIT_SUCCESS;
 }


