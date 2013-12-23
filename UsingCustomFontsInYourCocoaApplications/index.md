---
layout: page
title: UsingCustomFontsInYourCocoaApplications
---

**Important:** Apple Type Services for Fonts is deprecated in Mac OS X v10.6 and later. Please use Core Text instead.

----

I created a great little interface but didn't realize I had used some non system-fonts, so when other people launch it, it looks all whack.  How do I embed these in my application with xcode?

----

You could try copying them into your resources directory and see if they show up in General/NSFontManager's     availableFonts. I doubt that'll work though - just make a .tiff image of your text and show it that way.

*Comment: This is possibly the worst advice I've ever seen on this site. Never heard of localization?*

----

You can activate arbitrary fonts (e.g. in your Resources folder) with the Carbon call     General/ATSFontActivateFromFileSpecification(). Pass in     General/ATSFontContextLocal as the context if you want the font to be available only to your application.

----

If this type of application was distributed, would there be any copyright issues to worry about?

----

Yes, fonts are copyrighted software and you'd need permission to distribute them.

*Unless the font is in the public domain or under a license that would allow you to do so, of course.*

----
Taken from http://www.cocoabuilder.com/archive/message/cocoa/2005/1/16/125883, this loads all fonts from the application's bundle.

    
- (void)loadLocalFonts
{
   General/NSString *fontsFolder;    
   if ((fontsFolder = General/[[NSBundle mainBundle] resourcePath])) {
       NSURL *fontsURL;
       if ((fontsURL = [NSURL fileURLWithPath:fontsFolder])) {
           General/FSRef fsRef;
           General/FSSpec fsSpec;
           (void)General/CFURLGetFSRef((General/CFURLRef)fontsURL, &fsRef);
           if (General/FSGetCatalogInfo(&fsRef, kFSCatInfoNone, NULL, NULL, &fsSpec,
NULL) == noErr) {
               General/FMActivateFonts(&fsSpec, NULL, NULL, kFMLocalActivationContext);
           }
       }
   }
}


----

Does anyone know how to retrieve the General/PostScript name from an activated font -- so that you could use General/NSFont fontWithName:? The code above assumes that you  know the name of the font you're activating.

----

Okay, answering my own questions is fun. :) If you use General/ATSFontActivateFromFileSpecification instead of General/FMActivateFonts, you're given a font container which you can use with General/ATSFontFindFromContainer. This then gives you an array of General/ATSFontRefs, and then you can finally use General/ATSFontGetPostScriptName to obtain the individual typeface names. Isn't General/AppleTypeServices fun?

-- General/DustinMacDonald

----

General/FMActivateFonts is deprecated. it has been replaced with General/ATSFontActivateFromFileSpecification

    
- (void)loadLocalFonts
{
	General/NSString *fontsFolder;    
	if(fontsFolder = General/[[NSBundle mainBundle] resourcePath])
	{
		NSURL *fontsURL;
		if(fontsURL = [NSURL fileURLWithPath:fontsFolder])
		{
			General/FSRef fsRef;
			General/FSSpec fsSpec;
			General/CFURLGetFSRef((General/CFURLRef)fontsURL, &fsRef);
			if(General/FSGetCatalogInfo(&fsRef, kFSCatInfoNone, NULL, NULL, &fsSpec, NULL) == noErr)
			{
				General/ATSFontActivateFromFileSpecification(&fsSpec, kATSFontContextLocal, kATSFontFormatUnspecified, 
													 NULL, kATSOptionFlagsDefault, NULL);
			}
		}
	}
}


----
Could you perhaps load the font data using General/NSData and then use General/ATSFontActivateFromMemory instead of mucking around with General/FSRef<nowiki/>s?

----

General/ATSFontActivateFromFileSpecification is deprecated in Leopard.  Here's another version that uses General/ATSFontActivateFromFileReference and expects fonts in subdir (Resources/fonts).  Note: be sure to check "Preserve HFS Data" in your build settings if you are copying font suitcases to your resource directory.

    
- (BOOL)loadLocalFonts:(General/NSError **)err requiredFonts:(General/NSArray *)fontnames
{
	General/NSString *resourcePath, *fontsFolder,*errorMessage;    
  NSURL *fontsURL;
  resourcePath = General/[[NSBundle mainBundle] resourcePath];
  if (!resourcePath) 
  {
    errorMessage = @"Failed to load fonts! no resource path...";
    goto error;
  }
  fontsFolder = General/[[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"/fonts"];
  
  General/NSFileManager *fm = General/[NSFileManager defaultManager];
  
  if (![fm fileExistsAtPath:fontsFolder])
  {
    errorMessage = @"Failed to load fonts! Font folder not found...";
    goto error;
  }
  if(fontsURL = [NSURL fileURLWithPath:fontsFolder])
  {
    General/OSStatus status;
    General/FSRef fsRef;
    General/CFURLGetFSRef((General/CFURLRef)fontsURL, &fsRef);
    status = General/ATSFontActivateFromFileReference(&fsRef, kATSFontContextLocal, kATSFontFormatUnspecified, 
                                              NULL, kATSOptionFlagsDefault, NULL);
    if (status != noErr)
    {
      errorMessage = @"Failed to acivate fonts!";
      goto error;
    }
  }
  if (fontnames != nil)
  {
    General/NSFontManager *fontManager = General/[NSFontManager sharedFontManager];
    for (General/NSString *fontname in fontnames)
    {
      BOOL fontFound = General/fontManager availableFonts] containsObject:fontname]; 
      if (!fontFound)
      {
        errorMessage = [[[NSString stringWithFormat:@"Required font not found:%@",fontname];
        goto error;
      }
    }
  }
  return YES;
error:
  
  if (err != NULL) {
    General/NSString *localizedMessage = General/NSLocalizedString(errorMessage, @"");
    General/NSDictionary *userInfo = General/[NSDictionary dictionaryWithObject:localizedMessage forKey:General/NSLocalizedDescriptionKey];
    *err = General/[[NSError alloc] initWithDomain:General/NSCocoaErrorDomain code:0 userInfo:userInfo];
  }
  
  return NO;

}


This is how you might use it:

    
if (![self loadLocalFonts:&err requiredFonts:General/[NSArray arrayWithObject:@"General/BellGothicBT-Black"]])
{
  General/NSAlert *alert = General/[NSAlert alertWithError:err];
  [alert runModal];
  exit(0);
}


----

Snow Leopard has a new General/CTFontManager object for loading fonts. See General/CTFontManagerRegisterFontsForURL()
http://developer.apple.com/mac/library/documentation/Carbon/Reference/CoreText_FontManager_Ref/Reference/reference.html

----

There sure is a lot of code on this page for doing something that only requires using the General/ATSApplicationFontsPath Info.plist key.
http://developer.apple.com/mac/library/documentation/General/MacOSX/Conceptual/General/BPRuntimeConfig/Articles/General/PListKeys.html#//apple_ref/doc/uid/20001431-SW8
