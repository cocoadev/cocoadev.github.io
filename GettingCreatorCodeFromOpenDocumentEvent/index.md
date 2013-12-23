---
layout: page
title: GettingCreatorCodeFromOpenDocumentEvent
---



What I want to do is get the 4-character creator-code of the application that is requesting my application to open a document.  In my applicationWillFinishLaunching method, I set one of my methods to be a handler for the open-document event; in my handler method, I get the processSignature field of the ProcessInfoRec structure from the ProcessSerialNumber of the event like:

    
OSType senderSig = 0;

NSAppleEventDescriptor const *const aeDesc =
    [event attributeDescriptorForKeyword:keyAddressAttr];

NSData const *const psnData =
    aeDesc coerceToDescriptorType:typeProcessSerialNumber] data];

if ( psnData && [psnData length] == sizeof( [[ProcessSerialNumber ) ) { 
    ProcessSerialNumber const *const psn =
        static_cast<ProcessSerialNumber const*>( [psnData bytes] );
    ProcessInfoRec info;
    info.processInfoLength = sizeof( info );
    info.processName = NULL;
    info.processAppSpec = NULL;
    if ( ::GetProcessInformation( psn, &info ) == noErr )
        senderSig = info.processSignature;
}   


All this works great.  However, there's one problem: I get the correct creator-code only when my application is already running, i.e., if some other application tells my already-running application to open a document.  If my application is not already running, it first has to be launched.  In this case, the creator-code I get is my own -- not very informative.

So, in the case where my application is not already running, how do I get the right creator-code?

----
Are you able to consistently get an FSSpec, FSRef, path, or URL to the calling process? If so, you can use that to get the creator code.
----
What do u mean by creator code?? sorry to ask this

----

CreatorCode s are four-character codes from the days before Macs used file extensions. When applied to a file, they (along with another code, the type code) determined what application would open them and how they would be handled.

----
Creator codes are still used -- they're the value for CFBundleSignature in an application's Info.plist.  It uniquely identifies an application.

As to the FSSpec, etc., to the calling process.... well, if I knew what the calling process was, I'd already have my answer.  The parent process is always WindowServer.

----
After some more debugging, I've figured out that the event I'm getting looks like this:
    
{ 1 } 'aevt':  aevt/odoc (****'){
          return id: 1224278022 (0x48f90006)
     transaction id: 0 (0x0)
  interaction level: 112 (0x70)
     reply required: 0 (0x0)
             remote: 0 (0x0)
      for recording: 0 (0x0)
  target:
    <no target specified>
  fEventSourcePSN: { 0x0,0x72a0001 } (Adobe Lightroom)
  optional attributes:
    < empty record >
  event data:
    { 1 } 'aevt':  - 1 items {
      key '----' - 
        { 1 } 'list':  - 1 elements {
          { 1 } 'alis':  236 bytes {
            /Users/pjl/Pictures/Photographs/Panther Beach 2006/CRW_7900-Edit.tif
          }
        }
    }
}


My original code I believe had been getting the PSN from the "target" -- which in this case is empty.  So what I really want is the PSN from "fEventSourcePSN" but I'm unclear on the right incantation of Cocoa code to get at it.  Any ideas?
----
Quick search through the docs turned up     keyOriginalAddressAttr. What happens if you use this instead of     keyAddressAttr? (Worth a shot, right?) --JediKnil

----
I tried that -- it doesn't work, at least not doing a simple substitution.  FYI, the event that does make my app work (when it's already running) is:
    
{ 1 } 'aevt':  aevt/odoc (i386){
          return id: 1232732165 (0x497a0005)
     transaction id: 0 (0x0)
  interaction level: 112 (0x70)
     reply required: 0 (0x0)
             remote: 0 (0x0)
      for recording: 0 (0x0)
  target:
    { 1 } 'psn ':  8 bytes {
      { 0x0, 0x7380001 } (Adobe Lightroom)
    }
  fEventSourcePSN: { 0x0,0x7380001 } (Adobe Lightroom)
  optional attributes:
    < empty record >
  event data:
    { 1 } 'aevt':  - 1 items {
      key '----' - 
        { 1 } 'list':  - 1 elements {
          { 1 } 'alis':  236 bytes {
            /Users/pjl/Pictures/Photographs/Panther Beach 2006/CRW_7900-Edit.tif
          }
        }
    }
}

Note the "target": it has an embedded 'psn ' descriptor.  The value for fEventSourcePSN doesn't.  It seems to be just a raw PSN.  Assuming that's correct, I don't know the magic Cocoa incantation to get at it.

----
I believe that keyOriginalAddressAttr is what you want, but you're probably not hooking it early enough.  I don't know, but I might guess that you have gotten your AE debug output by setting the AEDebugReceives environment variable and launching by hand, in which case you miss the AppleEvent that actually launches your app in the first place.  If you put this line of code at the top of main():

    
    setenv("AEDebugReceives", "1", 1);


I think you'll find that the event is there and contains what you want, but by the time it gets to you, it has been repackaged somewhere under [NSAppleEventManager dispatchRawAppleEvent:withRawReply:handlerRefCon:] and now your fEventSourcePSN will be your app itself, not the original requestor.  It's been a little while, but I think I managed to get the original event by subclassing [NSApplication(NSAppleEventHandling) _handleCoreEvent:withReplyEvent:], which is a bad way since it's private API.  I also tried AEInstallEventHandler() from applicationWillFinishLaunching (I suspect that [NSAppleEventManager setEventHandler:andSelector:forEventClass:andEventID:] won't work for 'odoc' because it appears to be handled specially at a higher level in Cocoa).  That didn't work, and I haven't figured out why.  Possibly it's too late?  I tried the same approach at the beginning of main() and I think that was too early.

--TomBunch

----
I just conducted some experiments and realized I have made absolutely no contribution here, so nevermind.  When I subclass  [NSApplication(NSAppleEventHandling) _handleCoreEvent:withReplyEvent:] the event descriptor argument I receive when launching also has my local app's psn for the keyOriginalAddressAttr.  The event looks like you reported, the important part being:

    
  target:
    <no target specified>
  fEventSourcePSN: { 0x0,0xe0001 } (Finder)

keyEventSourceAttr doesn't seem likely to help.  It's supposed to return one of the following as far as I can tell.
    
enum {
  kAEUnknownSource              = 0,
  kAEDirectCall                 = 1,
  kAESameProcess                = 2,
  kAELocalProcess               = 3,
  kAERemoteProcess              = 4
};

I'll keep playing with it, but it may just be an Apple bug.  Have you filed a radar for it?

--TomBunch

