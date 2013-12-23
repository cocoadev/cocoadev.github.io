---
layout: page
title: LocatingTheDownloadFolder
---

Is it possible to locate the download folder in Cocoa (and/or Carbon) without having to delve in InternetConfig? None of the usual candidates (NSSearchPathForDirectoriesInDomains, FSFindFolder) do this, and InternetConfig makes it a pain by requiring me to use FSSpec -- a type that was marked as deprecated in the PowerPC -> IntelCore transition, thus making me worry about the portability of my code.
What should I do? -- EmanueleVulcano aka l0ne

----

The download folder is stored as data in a plist called com.apple.internetconfig.plist.  The data is an alias to the folder.  I realize this involves "devling into internet config", but it works fine for me.

----
I meant "delving into InternetConfig" as in "having to use the very old, half-deprecated InternetConfig APIs". Which I don't have to, thanks to you :).

Anyway, it's a NSDictionary plist with "Version X.X.X" [NSDictionary] > "ic-added" [NSDictionary] > "DownloadFolder" [NSDictionary] > "ic-data" [NSData], for everyone's future reference, where X.X.X is 2.5.4 in my case (but might reasonably vary) and is the only item in the root NSDictionary.

----

Keyword for google: Safari

----

I'm concerned that the above solution reads the Internet Config plist directly. This seems a little fragile to me. I've written the code to load it from IC using the APIs.
It isn't pretty but it seems safer.

Getting the downloads folder using IC:

    

- (NSString*)downloadsLocationFromIC
{
	NSString	*	result = nil;
	ICInstance		icInstance = {'\0'};
	
	if ( ICStart ( &icInstance, '????' ) == noErr )
	{
		OSStatus	error = noErr;
		ICAttr		attr = {'\0'};
		Handle		downloadFolderDataSpec = NewHandle(0);
		
		// Grab ICFileSpec data...
		error = ICFindPrefHandle ( icInstance, kICDownloadFolder, &attr, downloadFolderDataSpec );
		if ( error == noErr && downloadFolderDataSpec != NULL )
		{
			// How big is alias data?
			long	aliasSize = GetHandleSize(downloadFolderDataSpec) - sizeof(ICFileSpec) + sizeof(AliasRecord);
			
			// Make new handle to hold just the alias data (not the ICFileSpec header).
			AliasHandle	aliasHandle = (AliasHandle)NewHandleClear(aliasSize);
			if ( aliasHandle != NULL )
			{
				ICFileSpec	**	fileSpec = (ICFileSpec**)downloadFolderDataSpec;
				FSRef			ref = {'\0'};
				Boolean			wasChanged = FALSE;

				// Copy aliasrecord data into aliashandle
				HLock( downloadFolderDataSpec );
				HLock( (Handle)aliasHandle );
				memcpy( *aliasHandle, &((**fileSpec).alias), aliasSize);
				
				// Resolve and get path from URL....
				if ( FSResolveAlias( NULL, aliasHandle, &ref, &wasChanged ) == noErr )
				{
					CFURLRef url = CFURLCreateFromFSRef( NULL, &ref );
					if ( url != NULL )
					{
						result = [((NSURL*)url) path];
						CFRelease( url );
					}
				}
				
				DisposeHandle( (Handle)aliasHandle );
			}
			
			DisposeHandle( downloadFolderDataSpec );
		}
		
		ICStop ( icInstance );
	}
	
	return( result );
}



Enjoy!

