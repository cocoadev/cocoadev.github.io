---
layout: page
title: MoreAuthSample
---

MoreAuthSample is obsolete, see BetterAuthorizationSample.

----

http://developer.apple.com/samplecode/MoreAuthSample/

MoreAuthSample is Apple Sample Code that illustrates how to properly work with the Security framework to perform privileged operations using a small helper tool.  If you need to perform a privileged operation in your application or other code, you **must** follow its example in order to avoid opening privilege-escalation security holes.  If you don't understand **exactly** why MoreAuthSample is structured the way it is and how it works, you should not try to come up with a "simpler" way to do what you need.

*
It should be noted that reasonably smart people can read the comments in MoreAuthSample code and derive an understanding of why it is structured as it is.  Permission may then be granted to come up with a "simpler" way.  But only if you're really really smart.  :-)  For this will be a difficult task, grasshopper.
*

Also **never** use the techniques illustrated by MoreAuthSample to launch graphical applications in a privileged fashion. 

*
Graphical Cocoa applications are vulnerable to InputManager hacks.  There are well-documented techniques for inserting arbitrary code into the runtime of any Cocoa app by way of an InputManager.  You wouldn't want arbitrary code to be able to run from your Cocoa app running with root privs.  That would be bad.  See ProductSecurityStrategies for more discussion.
*

Only use them for very small, very constrained helper tools that can verify that they are being called in the context they are intended to be called and for the purpose they are intended to be used (which is a lot of why MoreAuthSample is as complex as it is).

----
I recently spent some time working with the MoreAuthSample code under Xcode 2.4.  

Here's a reimplementation of the <code>GetPathToSelf()</code> routine from <code>MoreSecurity.c</code> that uses public CoreFoundation API.  The original implemention used some now-deprecated-in-10.4 routines, and a seemingly private Apple function (<code>__NSGetExecutablePath</code>).

    
static int GetPathToSelf(char **pathToSelfPtr)
	// A drop-in replacement for GetPathToSelf() from MoreAuthSample's MoreSecurity.c,
	// implemented using CoreFoundation.
{
	int err = 0;
	
	assert( pathToSelfPtr != NULL);
	assert(*pathToSelfPtr == NULL);
		
	CFBundleRef mainBundle = CFBundleGetMainBundle();
	CFURLRef executableURL = NULL;
	CFStringRef pathStringRef = NULL;
	
	char *path = NULL;
	
	if (mainBundle != NULL) {
		executableURL =  CFBundleCopyExecutableURL(mainBundle);
		if (executableURL != NULL) {
			pathStringRef = CFURLCopyFileSystemPath(executableURL, kCFURLPOSIXPathStyle);
			if (pathStringRef != NULL) {
				CFIndex pathSize = CFStringGetLength(pathStringRef) + 1;
				path = calloc(pathSize,1);
				if (path != NULL) {
					Boolean gotCString = CFStringGetCString(pathStringRef, path, pathSize, kCFStringEncodingUTF8);
					 if (!gotCString) {
						 free(path);
						 path = NULL;
					 }
				}
			}
		}
	}
	
	*pathToSelfPtr = path;
	
	// Do the CF memory management.
	CFQRelease(executableURL);
	CFQRelease(pathStringRef);

	assert(*pathToSelfPtr != NULL);
	if (*pathToSelfPtr == NULL)
	{
		err = -1;
	}
	return err;
}


--BillGarrison


----

There's a byte-order bug in the file MoreSecurityTest.c in the currently (2006-Sep-11) downloadable project.  The fix is simple.

MoreSecurityTest.c, on or near line 357:

Original:      assert( (130 <= addr.sin_port) && (addr.sin_port <= 132) );

Fix:     assert( (130 <= ntohs(addr.sin_port)) && (ntohs(addr.sin_port) <= 132) );

--BillGarrison

