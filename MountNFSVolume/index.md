---
layout: page
title: MountNFSVolume
---

I have been trying in vain to mount an nfs volume though code. 

Essentially mounting comprises of running the following as root:

     mount_nfs -P thunder:/media/sdb5 /Users/user/Desktop/m 

There are various things I have tried so far.

1) Security Framework

Pretty much stock reference code here.

    

		AuthorizationFlags myFlags = kAuthorizationFlagDefaults;
		AuthorizationRef myAuthorizationRef;
				
		OSStatus myStatus = AuthorizationCreate(NULL, kAuthorizationEmptyEnvironment, myFlags, &myAuthorizationRef);
		if (myStatus != errAuthorizationSuccess) {
			NSLog(@"Failed to create authorization. Error %i",myStatus);
			return;
		}
		
		do
		{
			{
				AuthorizationItem myItems = {kAuthorizationRightExecute, 0, NULL, 0};
				AuthorizationRights myRights = {1, &myItems};
			
				myFlags = kAuthorizationFlagDefaults | kAuthorizationFlagInteractionAllowed |
					kAuthorizationFlagPreAuthorize | kAuthorizationFlagExtendRights;
				myStatus = AuthorizationCopyRights (myAuthorizationRef,&myRights, NULL, myFlags, NULL );
			}
			
			if (myStatus != errAuthorizationSuccess) break;
			{
				char myToolPath[] = "/sbin/mount_nfs";
				char * myArguments[] = {"-P","thunder:/media/sdb5","/Users/user/Desktop/m/", NULL };
				//char myToolPath[] = "/usr/bin/whoami";
				//char * myArguments[] = {NULL};
				FILE * myCommunicationsPipe = NULL;
				char myReadBuffer[128];
				
				myFlags = kAuthorizationFlagDefaults;
				myStatus = AuthorizationExecuteWithPrivileges(myAuthorizationRef, myToolPath, myFlags, myArguments, &myCommunicationsPipe);
				
				if (myStatus == errAuthorizationSuccess)
				{
					for(;;)
					{
						int bytesRead = read (fileno (myCommunicationsPipe),
											  myReadBuffer, sizeof (myReadBuffer));
						if (bytesRead < 1) break;
						write (fileno (stdout), myReadBuffer, bytesRead);
					}
				}
				
			}
		} while (0);
			
			AuthorizationFree (myAuthorizationRef, kAuthorizationFlagDefaults);
				
				if (myStatus) printf("Status: %ld\n", myStatus);


When run the following is returned:

[Session started at 2007-10-18 19:52:38 -0500.]
mount_nfs: /Users/user/Desktop/m: Permission denied

Hmm ok, yet running that command with sudo works...

2) Next thing I try is a simpler verson of this.

    
int main (int argc, const char * argv[])
{
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	{
		NSLog(@"%i",getuid());
		NSLog(@"%i",geteuid());
		NSString * command = @"mount_nfs -P thunder:/media/sdb5 /Users/user/Desktop/m";
		NSLog(@"%@",command);
		system([command UTF8String]);
	}
    [pool release];
    return 0;
}


Run by the following (with output):
    
$ sudo chmod 4775 ./nfs-shim 
$ sudo chown root: ./nfs-shim
$ ./nfs-shim
2007-10-18 19:33:22.754 nfs-shim[21209] 1000
2007-10-18 19:33:22.754 nfs-shim[21209] 0
2007-10-18 19:33:22.755 nfs-shim[21209] mount_nfs -P thunder:/media/sdb5 /Users/user/Desktop/m
mount_nfs: /Users/user/Desktop/m: Permission denied


I have the setuid'd executable run `whoami' and it returns root as it should so I'm pretty sure that mount_nfs is running as root.

3) Maybe it needs a shell? I change the command to @"sh -c \"mount_nfs -P thunder:/media/sdb5 /Users/user/Desktop/m\"" but I end up with the same result.

Maybe mount_nfs is not working via the effective UID... But I'm not sure.

Any other thoughts from the CocoaDev crowd?

Thanks in advance.

----
If I remember correctly, mount_* requires an *existing* folder to mount into, and cryptically uses a permissions error when it isn't there. But also, OS X users don't expect this kind of behavior when volumes are mounted; they expect them to show up in /Volumes. Are you sure there isn't a higher-level way to mount NFS volumes? (Like say, opening the URL "nfs://thunder/media/sdb5" or something?) --JediKnil

----

The folder does exist. It seems to be a permission error rather then a "not found" error. I tried the nfs URL scheme, but unfortunately it also gets permission denied from the finder. It seems the only way to mount it is to use the automounter with netinfo or the root command.

----

FSMountServerVolumeAsync works for programmatically mounting volumes in the Finder (I've used it for SMB and WebDAV, haven't tried NFS, but it works where -openURL does not). See http://developer.apple.com/documentation/Carbon/Reference/File_Manager/Reference/reference.html or search for the function in Apple docs. Here's my actual code (Don't worry about the "Owner" stuff, it's just a way my objects talk to each other.) --PaulCollins

    static void
MyVolumeMountCallback(FSVolumeOperation volumeOp, void *clientData, OSStatus err, FSVolumeRefNum mountedVolumeRefNum)
{
	NSError *nserr = nil;
	NSDictionary *dict = [(NSString *)clientData autorelease];
	id owner = [dict objectForKey:@"owner"];
	if (err != noErr) {
		NSDictionary *edict = [NSDictionary dictionaryWithObjectsAndKeys: [NSString stringWithFormat:@"File Manager error %d", err], NSLocalizedDescriptionKey, nil];
		nserr = [[[NSError alloc] initWithDomain:NSOSStatusErrorDomain code:err userInfo:edict] autorelease];
		NSMutableDictionary *mdict = [NSMutableDictionary dictionaryWithDictionary:dict];
		[mdict setObject:nserr forKey:@"err"];
		dict = mdict;
	}
    FSDisposeVolumeOperation(volumeOp);
       // man not need performSelectorOnMainThread here, I'm just paranoid
	[owner performSelectorOnMainThread:@selector(connectedToServer:) withObject:dict waitUntilDone:NO];
    return;
}

@implementation MyFileServer

+ (BOOL)mountURL:(NSURL *)aURL owner:(id)anOwner error:(NSError **)anErr {
	FSVolumeMountUPP volumeMountUPP = NewFSVolumeMountUPP(MyVolumeMountCallback);
	FSVolumeOperation volumeOp;
	OSStatus err = FSCreateVolumeOperation(&volumeOp);
	if (err == noErr) {
		NSDictionary *dict = [[NSDictionary alloc] initWithObjectsAndKeys:[aURL absoluteString], @"url", anOwner, @"owner", nil]; // released by callback
		err = FSMountServerVolumeAsync((CFURLRef)aURL, NULL, NULL, NULL, volumeOp, dict, 0, volumeMountUPP, CFRunLoopGetCurrent(), kCFRunLoopCommonModes);
		if (err != noErr) {
            FSDisposeVolumeOperation(volumeOp);
        }
	}
	// so far, errors seem to be through the callback, but we'll handle any we get here
	if (err != noErr) {
		NSDictionary *edict = [NSDictionary dictionaryWithObjectsAndKeys: [NSString stringWithFormat:@"File Manager error %d", err], NSLocalizedDescriptionKey, nil];
		*anErr = [[[NSError alloc] initWithDomain:NSOSStatusErrorDomain code:err userInfo:edict] autorelease];
		return NO;
	}
	return YES;
}

