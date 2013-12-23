---
layout: page
title: HowToGetTheKeyWindow
---



From a background app, I need to obtain ANY kind of handle to what the Apple documentation calls the "key window" of the "active application".  Not necessarily my own app; in fact almost never my app.

The overall problem that I'm solving is to build a function that uses a range of different options to try to determine the file path of the 'current' document, no matter what application, in order to manipulate that file's Spotlight meta data.  It won't always be possible but it will be useful in the cases where it is possible.  Some of the more common apps provide AppleScript commands that just cough up the current document's file path and for special cases like Word and Photoshop I'll use those commands.  I'm looking into more general options that I can fall back on when the current "active application" with focus doesn't provide a command, or I haven't coded a case for it.

The AccessibilityAPI might be disabled on the us er's system and is therefore a bad option.  There also are virtually no examples on how to use the AccessibilityAPI, so it's nearly useless.  I may use it as a last resort.

Many document-based applications will set a "proxy icon" in the document window that I can use to find the file itself.  Finding that icon has proven to be harder than I expected.  It surprised me that I can't even find a simple system function to tell me what application is currently active.  I'm looking at what I can get from the "Window Manager".

The Carbon functions ActiveNonFloatingWindow() and FrontNonFloatingWindow() seem to do what I need to get started, but in practice they don't seem to actually do anything.  Here's some code:

    
#import <Carbon/Carbon.h>

@implementation RPDocumentContext

+ (NSString *)getCurrentDocumentPath
{
	WindowRef rWindow = ActiveNonFloatingWindow();
	WindowRef rWindow2 = FrontNonFloatingWindow();
...


Both rWindow and rWindow2 are 0x0 when I look at them in GDB after those two function calls, so they seem to just not be returning anything useful at all.  I have Googled up other code that uses ActiveNonFloatingWindow in the same simple way, but when I do it from a Cocoa app it doesn't seem to work.

Should it work?  Is there a better way?

-endymion

----

No, it should not work. Normal Carbon/Cocoa calls will not do anything for you, because they only work within a single process.

Accessibility is really the way to go. Yes, it can be disabled, but this is why there's a switch: ask the user to turn it on.

If this really is unacceptable, then consider the use of ApplicationEnhancer. Be aware that users will probably be even more leery of that than they will of turning on Accessibility.

----

Thanks for the pointer.  Wow, I'm surprised.

If the Carbon calls aren't going to work then I'm going to use Apple Events, since that will always work.  It's so easy to do it in AppleScript:

    
tell application "System Events"
	set frontmostApplication to name of the first process whose frontmost is true
end tell


It blows my mind that it's so hard to do this from Cocoa.

----
As I said, Cocoa is made to work in *your* app, not in other people's app. The UNIX philosophy of separate process spaces and virtual memory means that other programs barely exist from the point of view of your own. Cocoa has no hooks into Accessibility so obviously Cocoa does not provide a way to do this. Cocoa does not do everything. It should no more blow your mind that it can't do this than it should blow your mind that you can't use it to write device drivers.

----

Interesting, noted.  In the spirit of giving something back, I hope to post my getCurrentApplication: and getCurrentDocumentPath: methods to this page once they work.  I'm having success with Apple Events using NSAppleScript, so I should have something soon.  Thanks again for the help.

----

As promised, here's example code for using AppleScript from a Cocoa app to get the current application's file path.  (That just happens to be what I need because I'm looking for the CFBundleIdentifier to use for keying special cases.)  You can just as easily get the current application's name, pid, or lots of other things, depending on the AppleScript string that you use.

    
+ (NSString *)getCurrentApplicationPath
{
    NSDictionary        *errorDict;
    NSAppleEventDescriptor  *returnDescriptor;

    // AppleScript to find the current application.
    NSAppleScript       *scriptObject = [[NSAppleScript alloc] initWithSource: 
@"tell application \"System Events\" \n \\

set theApplication to file of the first process whose frontmost is true \n \\

end tell"];

    // Run the AppleScript.
    returnDescriptor = [scriptObject executeAndReturnError: &errorDict];
    [scriptObject release];

    if([returnDescriptor descriptorType])
    {
        // The script execution succeeded.
        NSLog(@"Script executed sucessfully.");
        if(kAENullEvent != [returnDescriptor descriptorType])
        {
            // Apple Events send back a descriptor.  Turn it into an FSRef.
            returnDescriptor = [returnDescriptor
                coerceToDescriptorType:typeFSRef];
            FSRef *applicationFileRef = (FSRef*)returnDescriptor data] bytes];

            // Turn that [[FSRef into an NSString.
            NSString *strPath;
            CFURLRef url =
                CFURLCreateFromFSRef(kCFAllocatorDefault, applicationFileRef);
            if(url)
            {
                strPath =
                (NSString *)CFURLCopyFileSystemPath(url, kCFURLPOSIXPathStyle);
                [strPath autorelease];
                CFRelease(url);
            }
            else return nil;
            
            NSLog(@"Application path: %@", strPath);
            return strPath;
        }
        else NSLog(@"AppleScript has no result.");
    }
    else NSLog(@"Script execution error: %@",
                [errorDict objectForKey: @"NSAppleScriptErrorMessage"]);

}


-endymion

----

...on second thought, the above code really should just be:

    
NSDictionary *activeAppDict = [[NSWorkspace sharedWorkspace] activeApplication];
NSString *strApplicationPath = [activeAppDict objectForKey:@"NSApplicationPath"];
NSString *strApplicationBundleIdentifier = [activeAppDict objectForKey:@"NSApplicationBundleIdentifier"];


Oh well, live and learn...

I'm still not all the way to the active document's path from the active application but I'm having fun getting there at least.

-endymion

----

Hmph..  Well I thought that I was being slick by querying System Events using raw Apple Events (I'm still cheating and using NSAppleScript as in the above code to generate them at the moment) but I just discovered that my code doesn't work if "Enable access for assistive devices" is not enabled in the Universal Access tab of System Preferences.  So yes, like wise bamboo I will bend to the wind and I will use the AccessibilityAPI for this task.

I just really don't like the idea of a "MAKE THIS APP NOT WORK" option in the system preferences that's enabled by default.  What I have learned here may scuttle this little project as a potential marketable product because of that.  Oh well.

I was originally surprised that it's so hard to do this because Cocoa provides such simple access to most aspects of the system that my applications care about.  I assumed that, similar to in the Windows world, a Cocoa app would be able to query the Window Manager for information about the windows of other applications in the system.  This is the first big information firewall that I've slammed into with Cocoa, so far every other little tidbit of information that I have needed has been right there on a method provided by some NSSomething object.  I was thinking that I was just misunderstanding how to use NSWorkspace, or maybe that there was some kind of NSSystem above NSWorkspace that I could query for things like that.  I'm a very experienced developer but still a Cocoa newbie.  I can understand why Cocoa focuses on providing easy access only to concepts within a single application, sure.  Overall system stability and all of that.

I will post my AccessibilityAPI-based code here once it works.

-endymion

----

Meanwhile you can get the frontmost application by using the carbon API for processes: http://developer.apple.com/documentation/mac/Processes/Processes-24.html

----

Oh neat, thanks!  From all of this I've learned that the most Cocoa way to get that same information seems to be with the NSWorkspace:activeApplication method, as in the example above.  That gets you to the current active application, but I still don't see a way to go from there to a list of windows open in that application without using the AccessibilityAPI.

Also, I've learned more about the AccessiblityAPI and I like it less the more I learn.  If AXAPIEnabled() returns false (meaning that the user has not enabled Accessibility in System Preferences) then there is an API call to enable it programmatically called AXMakeProcessTrusted.  But the caveat is that it's broken, it has never worked, and it seems to still be broken in Leopard.  ( http://lists.apple.com/archives/carbon-dev/2006/Oct/msg00503.html ) ( http://lists.apple.com/archives/carbon-dev/2006/Oct/msg00503.html )  I'm really not sure why it's there in the first place.  So if you have an idea for an application that depends on features that are only provided through the AccessiblityAPI, then your idea is not ready for prime time because the AccessibilityAPI is not ready for prime time.  And also because not every app supports the AccessibilityAPI even when it's enabled.

If I were still dealing with Microsoft APIs then I would be paranoid right now and I would suspect that we're walled off from information about other apps and windows in the desktop to prevent third-party development of utility apps that compete with Expose and Spaces, not for security or stability reasons.  Apps like that require information about all open apps and windows in order to operate.

There has to be some way to find information about open windows in other applications, because of this screen saver: http://www.objective-cocoa.org/fenetresvolantes/en/index_en.html  I cracked open the bundle for that screen saver and looked at the Quartz composition inside, and the information about open windows is not coming from a Quartz Composer patch.  The Objective C executable is finding that information and sending it into the composition.  How... I have no idea.  I tried the screen saver with the AccessibilityAPI disabled and it still works, so it's getting that information in some other way.  Still investigating...

-endymion

----

Looks like that screensaver is using CoreGraphicsPrivate and a quick google finds http://paste.lisp.org/display/31828/ - but this is just window titles...
Meanwhile I've found the AccessiblityAPI to be pretty good so far. Once you get a hold of the topmost kAXURLAttribute or kAXDocumentAttribute in a window then you have your file. If I have some time tomorrow I'll dig up some code - RbrtPntn

Oh btw - if AXAPIEnabled() fails then its far easier in the short term to open the preference panel and do an alert panel with instructions for the user...

> OMG you're my hero!  Thank you so much for that.  How could you see that it was using CoreGraphicsPrivate?  I didn't know how to inspect the executable to learn more.

----

The reason the user must specifically enable the AccessibilityAPI is because it's obviously a huge privacy risk when it's enabled. 

----

Yes.  That's one of the reasons why I'm avoiding the AccessibilityAPI.  Why force my users to expose their entire system to spyware just so that my one app can do its thing?  Seems rude and irresponsible to bully my users into altering their System Preferences.  If the AXMakeProcessTrusted  mechanism worked, then I could make my app request authorization just for itself and not potentially for any random malware that comes along.

This entire project is just a little toy that I'm tinkering with in the lab to learn more about different aspects of the system.  Today I'm investigating using Services for finding the system-wide 'current document' context that I'm looking for.  Right now my pseudocode algorithm for a getCurrentDocumentPath() function looks like this:

    
strPath getCurrentDocumentPath() {

    // Step 1, try using application's AppleScript dictionary or maybe a
    // private API for special cases.
    if ( isInSpecialCaseList( applicationID = getCurrentApplicationBundleIdentifier() )
        if( strReturnPath = getCurrentDocumentPathFromSpecialCase( applicationID ) )
            return strReturnPath

    // Step 2, try system Services.
    if( strReturnPath = getCurrentDocumentPathFromSystemServices( applicationID ) )
        return strReturnPath

    // Step 3, try accessibility API.
    if( strReturnPath = getCurrentDocumentPathFromAccessibilityAPI( applicationID ) )
        return strReturnPath

    // Give up, many times there is no 'current document' context because something
    // like iChat is the current application.
    return nil
}


The structure has evolved into an entire class cluster so I can't really post all of it.  I will definitely post at least the AccessibilityAPI function once I get it working because there aren't enough good examples of how to use that, and this site could use one more.

-endymion

----
I get the name of the frontmost application perfectly with this carbon code:

    
ProcessSerialNumber psn = { 0L, 0L };
OSStatus err = GetFrontProcess(&psn);
/*error check*/

CFStringRef processName = NULL;
err = CopyProcessName(&psn, &processName);
/*error check*/
NSString* processNameString = (NSString*) processName;

Remember to CFRelease(processName) when you're done with it.
Code from: http://stackoverflow.com/questions/373020/finding-the-current-active-window-in-mac-os-x-using-python

----

I'm making an time tracking application without timers that gets the document path of the active window every 3 seconds. I wish I'd come across this page sooner, but I've developed what appears to be a fairly stable way of getting this information if this is still useful to folk.

1. I do it all using Apple Script. After wasting many hours messing around with Scripting Bridge, I realised it just wouldn't get various attributes I needed for windows, so I turned to the well established http://appscript.sourceforge.net/ wrapper. This has proved to be a total God send and I'd highly recommend it to anyone doing Apple Script stuff in a Cocoa app. It even has a method call that can return an error if it finds one when executing the Apple Script, which makes your code a lot cleaner.

2. Here's the Apple Script I'm basing all my application on:

    
tell application "System Events"
	set theprocess to the first process whose frontmost is true
	set thewindow to the value of attribute "AXFocusedWindow" of theprocess
	if value of attribute "AXRole" of thewindow is equal to "AXSheet" then
		set thewindow to value of attribute "AXParent" of thewindow
	end if
	set therole to value of attribute "AXRole" of thewindow as string
	set thesubrole to value of attribute "AXSubRole" of thewindow as string
	set thewindowtitle to value of attribute "AXTitle" of thewindow as string
	set thedoc to value of attribute "AXDocument" of thewindow as string
	set theproxy to value of attribute "AXProxy" of thewindow
end tell


3. Assuming the Enable Support for Assistive Devices is turned on (which I think is a small price to pay for something that *works*), this method works pretty reliably as far as I'm aware - I've been running prototypes and an Alpha of the app on my machine now for about 6 months without any observable issues. I've yet to do a proper round of hardcore soak testing, but so far it seems to be pretty positive. I've also got 10 people running my app without any real problems (except of course people forgetting to turn on the dreaded tick box in System Preferences.

4. I'm grabbing the window ID of the frontmost window using CGWindowListCopyWindowInfo, see my Stack Overflow question: http://stackoverflow.com/questions/311956/getting-a-unique-id-for-a-window-of-another-application

I'm keen to help others not make the same mistakes I've made in investigating window attribute grabbing from Cocoa using Apple Script, so please don't hesitate to get in contact if you need any advice. I'm a newbie to Cocoa, but I've been fiddling with this attribute grabbing stuff for about a year now, so I may be able to save you some time. 

You can get my contact details via my blog at http://www.synapticmishap.co.uk 

John Gallagher

