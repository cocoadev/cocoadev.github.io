---
layout: page
title: NSWorkspaceForOpeningWebPages
---



----

A common question that I am asked is how to load a webpage into a browser. Here you go:

    

-(IBAction)goToWebsite:(id)sender
{
    [[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:@"http://www.foo.com"]];
}


----
If you're into really creative (i.e. convoluted) Cocoa, try:
    
- (IBAction)goToWebsite:(id)sender
{
    [[[NSAppleScript alloc]
        initWithSource:@"do shell script \"open http://www.foo.com\""]
        executeAndReturnError:nil];
}

It's a lot easier than NSTask (sort of).

*
It's also a lot less efficient than using NSWorkspace, since it goes through a lot of different layers of the OS. This compiles and runs an AppleScript, which sends an AppleEvent to an OSAX, which launches your login shell (/bin/tcsh or whatever you've chosen), which then launches /usr/bin/open, which calls [[NSWorkspace sharedWorkspace] openURL:...]. 

It's true that efficiency probably doesn't matter all that much when doing something like this, but that's a whole lot of thrash. You should try to avoid going through so many different layers when you don't have to. It's easier just to call -[NSWorkspace openURL:] and trust AppKit to do the right thing. -- DrewThaler
*

----

How about:

    
- (IBAction)goToWebsite:(id)sender
{
    [[[NSAppleScript alloc]
        initWithSource:@"tell application \"Safari\" to open \"http://www.foo.com\""]
        executeAndReturnError:nil];
}


Slightly more efficient and it allows you to set the browser if you wish (if it supports AS).

----

That code leaks an NSAppleScript. :)

----
Well - You are ignoring the User's preferences there by specifying Safari - Using NSWorkspace will in turn use LaunchServices - which will use the correct Browser. --DiggoryLaycock

Indeed, but the user may have internal app preferences towards a certain browser.

----

The general advice is to reflect the user's system preferences (rather than the user having to tell every application which is their preferred browser). If you really want to launch with a different browser, call LS directly, like this (this code ignores errors, which is bad):

    
#include <CoreServices/CoreServices.h>
#include <ApplicationServices/ApplicationServices.h>

void LaunchURLWithBrowser( CFURLRef inURL, CFStringRef inBundleID )
{
    OSStatus 		err;
    LSLaunchURLSpec 	spec;

    err = LSFindApplicationForInfo( NULL, inBundleID, NULL, NULL, &spec.appURL );
    spec.itemURLs = CFArrayCreate( NULL, (const void **)&inURL, 1, NULL );

    spec.passThruParams = NULL;
    spec.launchFlags 	= kLSLaunchDefaults;
    spec.asyncRefCon 	= NULL;
    
    err = LSOpenFromURLSpec( &spec, NULL );
    
    CFRelease( spec.appURL );
    CFRelease( spec.itemURLs );
}

int main (int argc, const char * argv[]) {
    CFURLRef theURL = CFURLCreateWithString( NULL, CFSTR("http://www.apple.com"), NULL );
    LaunchURLWithBrowser( theURL, CFSTR("com.apple.safari") );
    LaunchURLWithBrowser( theURL, CFSTR("com.microsoft.explorer") );
    CFRelease( theURL );
    return 0;
}


-- FinlayDobbie

----

Is this page supposed to be about NSWorkspace or not? :-)

NSWorkspace, BTW, is present in a few other *Step implementations... Letting NSWorkspace do the heavy lifting is not only easier and more polite toward the user, but it's code-compatible with, for instance, GNUstep.

