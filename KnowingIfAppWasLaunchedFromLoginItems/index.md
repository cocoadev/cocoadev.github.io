---
layout: page
title: KnowingIfAppWasLaunchedFromLoginItems
---

To know if your application was launched because it is a login item, as opposed to being launched explicitly by the user from the Finder, use the code below:

    
+ (BOOL)wasLaunchedByProcess:(NSString*)creator
{
  BOOL  wasLaunchedByProcess = NO;

  // Get our PSN
  OSStatus  err;
  ProcessSerialNumber currPSN;
  err = GetCurrentProcess (&currPSN);
  if (!err) {
    // We don't use ProcessInformationCopyDictionary() because the 'ParentPSN' item in the dictionary
    // has endianness problems in 10.4, fixed in 10.5 however.
    ProcessInfoRec  procInfo;
    bzero (&procInfo, sizeof (procInfo));
    procInfo.processInfoLength = (UInt32)sizeof (ProcessInfoRec);
    err = GetProcessInformation (&currPSN, &procInfo);
    if (!err) {
      ProcessSerialNumber parentPSN = procInfo.processLauncher;

      // Get info on the launching process
      NSDictionary* parentDict = (NSDictionary*)ProcessInformationCopyDictionary (&parentPSN, kProcessDictionaryIncludeAllInformationMask);
      
      // Test the creator code of the launching app
      if (parentDict) {
        wasLaunchedByProcess = parentDict objectForKey:@"[[FileCreator"] isEqualToString:creator];
        [parentDict release];
      }
    }
  }

  return wasLaunchedByProcess;
}

+ (BOOL)wasLaunchedAsLoginItem
{
  // If the launching process was 'loginwindow', we were launched as a login item
  return [self wasLaunchedByProcess:@"lgnw"];
}


or, you may determine this through an Apple Event:
    
 (void) handleOpenApplicationEvent: (NSAppleEventDescriptor *) event replyEvent: (NSAppleEventDescriptor *) replyEvent 
{
    NSAppleEventDescriptor* propData = [event paramDescriptorForKeyword: keyAEPropData];
    bool startAtLogin = false;
    DescType type = propData ? [propData descriptorType] : typeNull;
    OSType value = 0;
    
    if(type == typeType)
    {
        value = [propData typeCodeValue];
        startAtLogin = (value == 'lgit'); //keyAELaunchedAsLogInItem
    }
    
    NSLog(@"PropDataTypeValue:%i startAtLogin:%i", value, startAtLogin );
}

- (void)applicationWillFinishLaunching:(NSNotification *)aNotification
{
    // install event handler
    [[NSAppleEventManager sharedAppleEventManager] 
     setEventHandler: self 
     andSelector: @selector(handleOpenApplicationEvent:replyEvent:)
     forEventClass:kCoreEventClass andEventID:kAEOpenApplication];


If you wish to determine if an application is amongst the user's current login items:

* 10.4+: use Apple's LoginItemsAE sample code (not 64 bit clean, deprecated APIs)
* 10.5+: use the APIs in LaunchServices/LSSharedFileList.h


These are two different tests, since an app can be a login item but still be launched from the Finder.

If you want to add an application to the set of login items, see StartingMyAppOnStartup.

