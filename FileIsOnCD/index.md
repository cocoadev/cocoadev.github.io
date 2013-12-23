---
layout: page
title: FileIsOnCD
---

Hi everybody,

does anyone know an elegant way to test if a file is on a CD/DVD-drive?

I've come so far as to find out about the "file system number":

    
  NSDictionary * fileAttributes;
  unsigned long fileSystemNumber;
  
  fileName = [fileName stringByResolvingSymlinksInPath];
  
  fileAttributes = [[NSFileManager defaultManager] fileSystemAttributesAtPath:fileName];
	
  fileSystemNumber = [fileAttributes fileSystemNumber];


What can I do with it? I have found absolutely no documentation about it.

The only thing I did find was a Carbon sample ( http://developer.apple.com/samplecode/CD-ROM_Detection/listing1.html ) which is quite cryptic and doesn't exactly do what I need.

any help appreciated.

----

Why do you care specifically about CDs? You can quite easily find out if a file is writable.

----

I agree that verifying whether or not the file
is on writable media is the easiest way to go.
I would suggest you check that the file exists
and then check if it is writable as
NSFileManager isWritableFileAtPath: will return NO
if the file does not exist.

    

BOOL isDir;
NSString *myPath = @"/MyVol/MyDir/Fonts/myFont.ttf";
NSFileManager *myManager = [NSFileManager defaultManager];
 
 
if ( [myManager fileExistsAtPath:myPath isDirectory:&isDir] == YES )
   if ( ( isDir == NO ) && ( [myManager isWritableFileAtPath:myPath] == NO ) )
      // Do something



I'd also have a look at getting the device byte capacity

see http://developer.apple.com/documentation/Darwin/Reference/KernelIOKitFramework/IOBlockStorageDevice/Classes/IOBlockStorageDevice/index.html

and if byteCapacity <= ( 800 * 1024 * 1024) (and not writable) odds are good that device is CD-ROM. byteCapacity < 8GB (and not writable) is probably DVD but this assumption is less certain :)

Hope this helps - DerekBolli

----

Just a stupid nit about naming wrt     myManager above. It's not *your* manager, it's *the* system NSFileManager.     defaultManager or     theManager would be better names, IMO.

----

If we're going to nit-pick.. I don't think there's anything wrong with the "my".  Past the line where the variable is set, he doesn't care where the manager came from, it's just the manager he's using.  If he used     defaultManager and he were, for some reason, to decide to use a file manager obtained in some other way then he'd have to change the variable name.

If I were doing it I'd just call it     fileManager.

*Well, that's kinda the point. There **is** no other (proper) way to get a NSFileManager instance.*

----

If Apple caught you using     myManager they might sue.

----

Good point, good point... wait, I don't even get the joke. :-) (Not that I want you to explain it, but just so you know.)

----

Another note: The path to a volume is not "/myVol", it's "/Volumes/myVol". While determining whether or not something is writable, be sure to put in a contingency for a directory on the volume...just in case you pick an existing filename. It appears that Cocoa has no explicit way of determining whether or not a volume is a CD...though it is obviously possible somehow as Carbon can do it and it shows up with a different icon in the Finder... Anyway, the fileSystemNumber appears to be a unique hash assigned to the volume, as it often looks like a random number in the millions. Remember that a volume is just a place that holds stuff: partitioned hard drives and multi-session discs count as multiple volumes, and so do mounted disk images. Could you give us an explanation of why this is needed?

--JediKnil

----

