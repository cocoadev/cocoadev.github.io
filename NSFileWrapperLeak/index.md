---
layout: page
title: NSFileWrapperLeak
---

I've got a little snippet of code, and it always seems to show a leak in MallocDebug whenever I run it.  And of course, I can't figure out what I'm doing wrong.

Here's the code I'm using (I just throw it in the main method of a generic Cocoa App):
    
#import <Cocoa/Cocoa.h>

void makeLeak() {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
    
    NSString *rtfPath = @"/Developer/Examples/DiscRecording/ObjectiveC/EnhancedDataBurn/ReadMe.rtfd";
    
    [[[NSFileWrapper alloc] initWithPath:rtfPath] autorelease];
    
    [pool release];
}

int main(int argc, const char *argv[])
{
    makeLeak();
    return NSApplicationMain(argc, argv);
}



And in MallocDebug I get this:

    
16 bytes in 1 node: 
0x1
_dyld_start
_start
main
makeLeak
-[NSFileWrapper initWithPath:]
+[NSFileAttributes attributesAtPath:traverseLink:]
_NS_FSPathMakeRef
_NSSoftLinkingGetFrameworkFuncPtr
_NSSoftLinkingLoadFrameworkImage
bytesInEncoding
-[NSString(NSStringOtherEncodings) getBytes:maxLength:filledLength:encoding:allowLossyConversion:range:remainingRange:]
__CFStringEncodeByteStream
CFPreferencesGetAppBooleanValue
CFPreferencesAppBooleanValue
CFPreferencesCopyAppValue
_CFStandardApplicationPreferences
_CFApplicationPreferencesSetStandardSearchList
_CFPreferencesIsManaged
_CFGetUserName
_CFUserName
_CFUpdateUserInfo
CFStringCreateWithCString
__CFStringCreateImmutableFunnel3
_CFRuntimeCreateInstance
NXZoneMalloc


Anyone want to slap me around a little bit and point out what I'm doing wrong?  Or maybe verify this?
- GusMueller

----

An interesting problem, but I can't duplicate it. I put basically the same code in my     main() and could not find any leaks using     leaks on the command line.

It looks to me like the leak comes when Cocoa tries to call FSPathMakeRef and has to load Carbon, or whatever framework has it. If you add a loop so that you make a bunch of file wrappers, do you get more leaks? If you only ever get one leak, then it's not a big deal, and probably not your fault. It's very strange, though; I can verify that the chain of functions you list is being called in the debugger (I didn't verify all of them, but up to CFPreferencesGetAppBooleanValue it's the same) but I don't see the leak. I'm running OS X 10.3.1, maybe this is very version-specific. -- MikeAsh

Ok, adding on a bit more info, since I don't feel like editing the above. If I run my test app in the debugger with ZeroLink turned off and MallocStackLogging *not* set, I get a single 16-byte leak (even though I create 100 NSFileWrappers). I can't duplicate the leak outside of the debugger or with MallocStackLogging set. Very strange.
----
Probably some one-shot allocation that is cached up in the internals and reused for the life of the program. I wouldn't worry about it to much.
----
Running it in a loop, I still only get 16 bytes leaked, so I guess it's not a big deal.  It is annoying though.  -- GusMueller

