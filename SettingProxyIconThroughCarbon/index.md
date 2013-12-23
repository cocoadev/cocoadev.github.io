---
layout: page
title: SettingProxyIconThroughCarbon
---

All right here is my predicament this time, originally I was using -setRepresentedFilename: to set the proxy icon for my program, however it leaks and naturally this isn't acceptable! Thus I have tried to go into carbon to get what I need done, but I have failed, here is what I have attempted:

    		NSURL * URLPath = [NSURL URLWithString:@"/Applications"];
		FSRef fsref;
		FSSpec fsspec;
		
		CFURLGetFSRef((CFURLRef)URLPath,&fsref);
		
		FSGetCatalogInfo(&fsref, 
						 kFSCatInfoNone, 
						 NULL,
						 NULL,
						 &fsspec,
						 NULL);
		
		
		
		void * ref = self window] windowRef];
		[[OSErr err;
		IconRef proxyIcon;
		SInt16 label;
		
		err = GetIconRefFromFile(&fsspec,
						   &proxyIcon,
						   &label);
		
		if (err != noErr)
		{
			NSLog(@"error!");
		}
		
		err = SetWindowProxyIcon(ref,proxyIcon);
		
		if (err != noErr)
		{
			NSLog(@"error!");
		}


If anyone can spot the error you'd make my day.

----

Doesn't this only work for Carbon windows?

----

Cocoa has a compatibility method -windowRef... but never-mind folks, this leaks also (unless you know the fix for that also heh...).

----
You'll need to call ReleaseIconRef on proxyIcon

----
AFAIK you have to call GetIconRefFromFileInfo instead of GetIconRefFromFile in OS X.

