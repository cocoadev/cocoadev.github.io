---
layout: page
title: SimpleJavaLauncher
---



I'm trying to write a simple Java launcher program.  I've been looking at some of Apple's sample code.  This is my first Objective C and Cocoa program so please forgive any newbie questions.

The program uses the JNI invocation interface to start a JVM in a seperate thread.  To obtain the necessary parameters to start the JVM, my code reads the app's <Java> dictionary in the Info.plist file.  It's much easier to read the Info.plist file in Objective C.  Also, if anything goes wrong (bad Info.plist, missing information, not enough memory, etc.), I want the launcher program to display an alert dialog box, have the user click "Quit", and then exit.

A lot of the example Cocoa code using Objective C assumes that one is writing a full-blown application complete with .nib files and such.  As such their main() functions are just:

    
int main( int argc, char *argv[] ) {
    return NSApplicationMain( argc, argv );
}


But I don't think that will work for me (not to mention that it's not at all clear how NSApplicationMain() calls the rest of one's program to run it).

Instead, my program basically is like:

    
int main( int argc, char *argv[] ) {
    [NSApplication sharedApplication];
    readInfoPlist();
    // create a new POSIX thread for the JVM
    // spawn JVM thread
    [NSApp run];
}


I have no idea whether calling [NSApplication sharedApplication] is the right thing to do, but the code works better having it.

The readInfoPlist() method is in Objective C.  It's basically:

    
void readInfoPlist() {
    NSAutoreleasePool *pool = [NSAutoreleasePool alloc] init];
    // read Info.plist
    [pool release];
}


And that's where my trouble starts.  Specifically, the application crashes on the [pool release].  The readInfoPlist() method does copy (via C++'s new operator and strcpy()) all needed data to a C++ data structure so the release shouldn't be deleting stuff I need later.  If I don't create/release any pool at all, then I get a lot of warnings about there not being a pool in place and that memory is leaking.

I also have my own myAlert() method that's:

    
void myAlert( char const *msg ) {
    NSAlert *const alert = [[NSAlert alloc] init];
    [alert setMessageText:[NSString stringWithUTF8String:msg]];
    [alert addButtonWithTitle:@"Quit"];
    [alert setAlertStyle:NSCriticalAlertStyle];
    [alert runModal];
    [alert release];
    ::exit( 1 );
}


In my case, all alerts are fatal, so, ideally, I'd like myAlert() simply to exit.  Also note that myAlert() can be called either from the original thread or the JVM thread.

In the case where the JVM is successfully launched, the application runs; then, if the user quits, the program is "stuck" presumebly because it's still sitting in [NSApp run].

So, assuming you've read all this, my questions are:


*What's the correct way to initialize a Cocoa application such as mine?
*How do I get the NSAutoreleasePool thing to work?
*Is the code for myAlert() correct?
*Is it OK for myAlert() to call exit()?
*It is OK for myAlert() to be called by any thread?
*How to I break out of [NSApp run] (assuming I should have called it in the first place)?


Thanks much in advance!

- Paul

----

For the NSAutoreleasePool, put that inside your main() function, not inside others. Then you won't have to worry about that. myAlert() is leaking because it's not inside an NSAutoreleasePool (unless you call it from readInfoPlist.

To exit a Cocoa app, use     [NSApp terminate:nil] - NSApp is short for     [NSApplication sharedApplication]

----

But why doesn't use of an NSAutoreleasePool work anywhere?  Ideally, main() shoudn't have to know or care about how readInfoPlist is implemented.

