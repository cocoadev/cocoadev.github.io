---
layout: page
title: DocumentBasedAppOpenLastFileonStartup
---



I have a document based app, which opens a blank document when I start it.  

Is there any way to make it open the last file saved on startup, rather than a new/blank file?   It seems like that would be a commonly desired behavior, but I haven't been able to find any way to do it.    I did find "applicationOpenUntitledFile", to control whether a new file is opened, but not a way to say open the last file saved.

----

Not too difficult. In your app delegate:

    
- (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender
{
    id dc = [NSDocumentController sharedDocumentController];
 
    // Reopen last document if user prefers
    if (preferencesSayToLoadTheLastDocument && 
        dc recentDocumentURLs] count] > 0)
    {
  	 // Only open the first in the list if it exists
	 if ([[[[NSFileManager defaultManager] fileExistsAtPath:[dc recentDocumentURLs] objectAtIndex:0] path)
        {
            if ([dc openDocumentWithContentsOfFile:[dc recentDocumentURLs] objectAtIndex:0] path] display:YES])
                return NO; // Doc open succeeded, return no, shouldn't open untitled file
        }
    }

    return YES;
}


*Corrected - I used code from an old project of mine and the newer method I use is better. :-)  You could improve it by enumerating the list ... that way if the 'fileExistsAtPath:' fails, you can keep going back to the previous document until you find one that still exists.*

----

That worked great, thanks for the response.

----

Just note the "if user prefers" part. Not everybody wants this non-standard behavior. In fact, many prefer otherwise, which is probably a good reason to have it off by default, too. :-)
----
Few things updated for OS X 10.4, since openDocumentWithContentsOfFile:display: has been deprecated. Here's a new version that seems to work. (Since it took me a little while to figure this part out, edit [[MainMenu.nib in IB, click on classes, add a class "MyAppDelegate," instantiate it, click on Instances, control-drag between File's Owner and myAppController, press Apple-2, and set choose delegate for this outlet connection. add the files to XCode, then add this code to MyAppDelegate.m.) I'm a novice, so please fix any errors if you find them!

    
- (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender
{
	id myDocumentController = [NSDocumentController sharedDocumentController];
	
	// Reopen last document if user prefers
	// For now, ignore any preference setting since I haven't coded for them yet. This routine is largely from www.CocoaDev.com
	int preferencesSayToLoadTheLastDocument = 1;
	if (preferencesSayToLoadTheLastDocument && 
		myDocumentController recentDocumentURLs] count] > 0)
	{
		NSURL *myURL = [[[NSURL alloc] init] autorelease];
		[[NSEnumerator *theRecentFilesEnumerator = [myDocumentController recentDocumentURLs] retain] objectEnumerator];
		while (myURL = [theRecentFilesEnumerator nextObject])		
		{
			if ([[[[NSFileManager defaultManager] fileExistsAtPath:[myURL path]])
			{
				if ([myDocumentController openDocumentWithContentsOfURL:myURL display:YES error:nil])
				{
					return NO;		// There was a live file somewhere, so don't open a new document.
				}
			}
		}
	}	
	return YES;
}

-dan


This is a lot simpler, you don't need to alloc the myURL, since it's just pointing into the array. Also, you can use a simple for loop on an NSArray, XCode even makes one for you with tab completion. 
    
- (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender
{
	id myDocumentController = [NSDocumentController sharedDocumentController];
	
	// Reopen last document if user prefers
	// For now, ignore any preference setting since I haven't coded for them yet. This routine is largely from www.CocoaDev.com
	int preferencesSayToLoadTheLastDocument = 1;
	if (preferencesSayToLoadTheLastDocument && 
		myDocumentController recentDocumentURLs] count] > 0)
	{
		NSURL *myURL;
                unsigned int i, count = [urlArray count];
                for (i = 0; i < count; i++) 
		{
                    myURL = [urlArray objectAtIndex:i];

			if ([[[[NSFileManager defaultManager] fileExistsAtPath:[myURL path]])
			{
				if ([myDocumentController openDocumentWithContentsOfURL:myURL display:YES error:nil])
				{
					return NO;		// There was a live file somewhere, so don't open a new document.
				}
			}
		}
	}	
	return YES;
}



----

Make sure AppController instance is in MainMenu.nib, not MyDocument.nib. I've just made this stupid mistake... -- DmitryChestnykh

Also, here's 10.5 version:

    
- (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender;
{
	id documentController = [NSDocumentController sharedDocumentController];
	
	// Reopen last document
	for (NSURL *url in [documentController recentDocumentURLs]) {
		if ([[NSFileManager defaultManager] fileExistsAtPath:[url path]]) {
			if([documentController openDocumentWithContentsOfURL:url display:YES error:nil])
				return NO;
		}
	}
	return YES;
}


----

Isn't this the wrong place for the code? Since now, if the user closes the document and wants to open a new one, the existing document is opened again. I think this code should go into willFinishLaunching, or am I wrong? --wackazong

