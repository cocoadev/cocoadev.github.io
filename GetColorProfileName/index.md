---
layout: page
title: GetColorProfileName
---

 

Given a NSBitmapImageRep, outputs the name of the color profile (if any):

    
- (NSString *)getColorProfileName:(NSBitmapImageRep *)theBitmap
{
  NSData              *colorSyncProfile = nil;
  NSImage             *finalImage = nil;
  CMProfileLocation   profileLocation;
  CMProfileRef        sourceProfile;
  CFStringRef         profName;

  colorSyncProfile = [theBitmap valueForProperty:NSImageColorSyncProfileData];
  if (colorSyncProfile)
  {
    profileLocation.locType = cmPtrBasedProfile;
    profileLocation.u.ptrLoc.p = (Ptr)[colorSyncProfile bytes];
  }

  CMOpenProfile(&sourceProfile, (colorSyncProfile) ? &profileLocation : NULL);
  profName = CopyProfileDescriptionCFString(sourceProfile);
  CMCloseProfile(sourceProfile);
  NSLog(@"ColorSyncProfileName: %@", profName);
  return [(NSString *)profName autorelease];
}


Then, using a function provided by http://developer.apple.com/qa/qa2001/qa1205.html,

    
CFStringRef CopyProfileDescriptionCFString(CMProfileRef prof)
{
    Str255         pName;
    ScriptCode     code;
    CFStringRef    str = nil;
    CMError        err;


    // for v4 profiles, try to get the best localized name from the 'desc'/'mluc' tag
    err = CMCopyProfileLocalizedString(prof, cmProfileDescriptionTag, 0,0, &str);
    // if that didn't work...
    if (err != noErr)
    {
        // for Apple's localized v2 profiles, try to get the best localized name from the 'dscm'/'mluc' tag
        err = CMCopyProfileLocalizedString(prof, cmProfileDescriptionMLTag, 0,0, &str);
        // if that didn't work...
        if (err != noErr)
        {
            // for normal v2 profiles, get the name from the 'desc'/'desc' tag
            err = CMGetScriptProfileDescription( prof, pName, &code);
            // convert it to a CFString
            if (err == noErr)
            {
                str = CFStringCreateWithPascalString(0, pName, code);
            }
        }
    }
    return str;
}


StephanBurlot

