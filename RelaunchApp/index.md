---
layout: page
title: RelaunchApp
---

What is the best way to relaunch my app programmatically? Can it be done through running an applescript and then terminating and the applescript waits a couple seconds and launches my app again, or does the applescript go down with the ship? Any proven methods that work for you people?

--ZacWhite

----
I have written some code to perform this task. Have a look at this blog entry: http://0xced.blogspot.com/2006/06/relaunch-your-cocoa-application-by.html (Relaunch your Cocoa application by itself)
Note that this is what is used by SparkleUpdater.

-- C�dric Luthi

----
Haven't tried it, but one idea could be to put a flag somewhere in the code (haven't figured out where) that says whether or not the application really terminated. Then you can do a fake termination by calling     [NSApp terminate], and rewrite your main method like this. WARNING: UNTESTED! --JediKnil
    
static BOOL shouldKeepGoing = YES;

int main(int argc, const char *argv[])
{
    int returnCode = 0;
    while (shouldKeepGoing) {
        shouldKeepGoing = NO;
        // Set it to YES somewhere in your code to restart the program.
        returnCode = NSApplicationMain(argc, argv);
    }
    return returnCode;
}


What if your app were to choke and croak? How could you relaunch after a crash? Is that doable? -- JasonTerhorst

----

How I do this is my program calls a simple Cocoa tool (command line app) that quits the program, and then launches it, and then that program finishes nomrally. I just use NSFileManager's openFile to open the app. And since my app is a little helper app, it is located inside the Resources folder, so it knows how to access the main program's path.

----

Take a look at the     LSOpenFromURLSpec function. You pass it a     LSLaunchURLSpec, which has a     launchFlags field in which you can specify     kLSLaunchNewInstance which will cause it to launch a new copy of an app that's already running. Basically, (1) call LSOpenFromURLSpec and pass a file URL to your     [[NSBundle mainBundle] bundlePath], then (2) call     [NSApp terminate:nil] to kill the current instance of your application.

Updated with code:

    
#include <ApplicationServices/ApplicationServices.h>

...

NSURL *url = [NSURL fileURLWithPath:[[NSBundle mainBundle] bundlePath]];

LSLaunchURLSpec launchSpec;
launchSpec.appURL = (CFURLRef)url;
launchSpec.itemURLs = NULL;
launchSpec.passThruParams = NULL;
launchSpec.launchFlags = kLSLaunchDefaults | kLSLaunchNewInstance;
launchSpec.asyncRefCon = NULL;

OSErr err = LSOpenFromURLSpec(&launchSpec, NULL);
if (err == noErr) {
    [NSApp terminate:nil];
} else {
    // Handle relaunch failure
}


If you want to be able to detect a crash and relaunch after that, though, you need to have another program running that spawns the app and either polls every X seconds to make sure it's still running, or waits for a notification of its termination and relaunches it.

----

I tend to go with the applescript app method.  I have a small applescript application bundle (must be bundle, simply having an app causes Classic to launch on my machine for some reason) inside the resources folder.  I then use NSWorkspace's launchApp: to launch it.  The applescript is really simple:

    
tell app "x"
    quit
    delay 1
    activate
end tell


You can catch crashes inside your app by using "signal(SIGILL, handleCrash)" where handleCrash is the name of a normal C-style function.  The function will take one parameter that is the type of crash that occurred.  You'll have to register for several signals.  A list of them can be found in /usr/include/sys/signal.h.  I'd, however, hate to have an app relaunch after a crash, especially since the crash reporter will offer to relaunch an application for you on Tiger.

----
Note that restarting your app from a signal handler is really hard to do correctly, see SignalSafety. It may work correctly a large part of the time, but it will crash and burn or freeze at least some of the time, which will significantly worsen the experience. If you want to catch a crash, it's much better to have an external process the way Apple and various third-party crash catchers do it.

