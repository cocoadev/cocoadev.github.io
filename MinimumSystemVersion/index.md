---
layout: page
title: MinimumSystemVersion
---

You can set the minimum required system version in your application's Info.plist, the key being "LSMinimumSystemVersion". The list of available keys are on this page : http://developer.apple.com/documentation/MacOSX/Conceptual/BPRuntimeConfig/Articles/PListKeys.html.

Note that LSMinimumSystemVersion is completely broken on Panther, making it more or less useless if you require 10.4 and up, and has various other bugs as well which make it useless in most situations.

----

When I tried compiling this with my app on 10.4.8, and then running it on a machine with 10.4.7, it simply crashes (with an output in the console behind the scenes). Is there a more graceful way of doing this that tells the user "Sorry, you need the latest version of OS X... here's a link to the latest updater patch, or a place to buy it on Amazon.com", and then offers a button or two for the user to choose to go to a web site or quit?

----

In main(), you could do a Gestalt() OS version check prior to calling NSApplicationMain, as in this example (warning, untested!)

    
static __inline__ NSString *PackedVersionToString(long ver) {
	return [NSString stringWithFormat: @"%x.%x.%x", ((ver & 0xFF00) >> 8), ((ver & 0x00F0) >> 4), (ver & 0x000F)];
}

static void EnsureMinOSVersion(long packedMinVersion) {
	long ver;
	if (noErr == Gestalt(gestaltSystemVersion, &ver)) {
		if (ver < packedMinVersion) {
			NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];

			/* ensure AppKit is up and running */
			NSApplicationLoad();

			/* tell user it's a no-go */
			NSRunAlertPanel(
				NSLocalizedString(@"This computer cannot run this program until its operating system is upgraded.", nil), /* message text */
				NSLocalizedString(@"This computer is currently running Mac OS X %@. It needs to be updated to Mac OS X %@.", nil), /* info text */
				NSLocalizedString(@"Quit", nil), /* button 1 */
				nil, /* button 2 - unused */
				nil, /* button 3 - unused */
				PackedVersionToString(ver),
				PackedVersionToString(packedMinVersion)
			);

			[pool release];

			/* exit application */
			exit(0);
		}
	}
}

int main(int argc, char *argv[]) {
	EnsureMinOSVersion(0x1048);
	NSApplicationMain(argc, argv);
	return 0;
}


Note that this code might not be able to protect against issues related to linking, compiler versions, etc. However, it should work for Mac OS X 10.4.7/10.4.8.

