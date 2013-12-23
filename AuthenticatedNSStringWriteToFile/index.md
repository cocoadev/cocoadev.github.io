---
layout: page
title: AuthenticatedNSStringWriteToFile
---

Is there a way to let strings be written to the library folder, with user authentication beforehand?

----

Uh ... do you mean is there a way to authenticate a user to allow writing to the main /Library folder? If so, then yes. There is. (If you want a more specific response, please pose a more specific question.)

----

Yes that's exactly what I mean.  How would you go about authenticating a user to allow writing to the main /Library folder?  Sorry about the lack of specifics.

----

Ah. In that case, you want the SecurityFramework and SecurityInterface. You basically request permission to do something using the security framework (by displaying the authentication dialog) and use that elevated privilege to do something (like save to a file your user would otherwise not be able to access. Check out those two topics. There's a wealth of info out there to help you.

----

I'll post some code here but I don't know how good it is.  It merely worked for me.  Comments are appreciated.

    
//Write out string
NSString *path = @"path/to/writable/location"; // try writing to a temporary directory
[myString writeToFile:path atomically:YES];

//Set up simple rights
AuthorizationRef myAuthorizationRef;
OSStatus myStatus;
myStatus = AuthorizationCreate (NULL, kAuthorizationEmptyEnvironment, kAuthorizationFlagDefaults, &myAuthorizationRef);

/* 
Apple recommends breaking the part of your app that needs the authorization off into a command line tool.
For these purposes (simply moving files), we can use the standard Unix "mv" (move) command to move
files that are written into a writable folder into a system-owned folder.  This keeps us in line
with Apple's recommendation and also makes the authorization process a lot simpler.  We also get out of
writing our own CLI application, so we win all around.
*/
if (myStatus == noErr)
{
    char *args[4]; // arguments for the mv command
    args[0] = "-f"; // force the mv so it won't ask for confirmation
    args[1] = (char *)[path UTF8String]; // path of file to move
    args[2] = "/Library/"; // location to move to
    args[3] = NULL; // terminate the args

    myStatus = AuthorizationExecuteWithPrivileges(myAuthorizationRef, "/bin/mv", 0, args, NULL);

    if (myStatus != noErr)
        NSLog(@"Error moving file");
    else
        NSLog(@"Success!");
}


That should work, but I've rarely had a problem writing to the /Library/ file.  If you are thinking of writing to /System/Library/, don't.  Period.  Other than that, consider whether it would be better to just write to ~/Library/.  That'll make things easier and let any users on the computer have their own copies.  Of course, if the info needs to be shared across every user, /Library/ is the place to put it.

As an afterthought, you could write the file to ~/Library/ and then "cp" into /Library/ (just change "/bin/mv" to "/bin/cp").  That would give you a backup in the user's library in case something happened.  If you couldn't locate the file in /Library/, you could then try ~/Library/.  Just a thought.

