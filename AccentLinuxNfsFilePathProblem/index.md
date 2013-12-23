---
layout: page
title: AccentLinuxNfsFilePathProblem
---

I�m working with existing code that opens audio files for analysis using Cocoa.  I have a problem opening files that have accented characters in them, but only when the file resides in an nfs mounted folder and only if the nfs mounted folder is hosted from a Linux machine (A<nowiki/>ccentLinuxNfs).  The only other host I�ve tested was another Mac.

I have noticed that finder also has a problem with A<nowiki/>ccentLinuxNfs files. I can�t copy these file by dragging and dropping it to my desktop, and finder does not display their name correctly.  I can, however,  copy the files from a console window if I use wild cards to avoid the accented characters. I can also run �sum� on these files from a console window and get the correct results.  The exact same file on a Mac hosted nfs mounted folder, and on a local folder can be opened with no problems and also display almost correctly and drag and drop/copy correctly in the browser.  All files without the accented characters in the L<nowiki/>inuxNfs folder also have no problems.

The code fails on a call to CFS<nowiki/>tringCreateWithCString (see example code) which returns NULL.  I am using kCFStringEncodingUTF8 encoding, which I think is correct.  I have tried (all) other encodings and have been able to get a valid CFS<nowiki/>tringRef back, but this always fails downstream.  The CFS<nowiki/>tringRef is passed to CFURLC<nowiki/>reateWithFileSystemPath to produce a CFURLRef (which is never NULL).  This is passed to CFURLG<nowiki/>etFSRef which always returns false for A<nowiki/>ccentLinuxNfs files.

I�m guessing that the file names are getting presented in a way that Cocoa tools can not hantle, but unix programs like �sum� can handle them so there should be a way to open the file.  The file name is being converted to FSS<nowiki/>pec so it can be used by O<nowiki/>penMovieFile(FFS<nowiki/>pec*,short*,SI<nowiki/>nt8), so if there is another way to get there, that can handle the A<nowiki/>ccentLinuxNfs, then that would work too.

Attached is my test program, followed by a console session which shows the problem. The file name of the mp3 file is �Queensr�che -Empire-01-Best I Can.mp3�, you can rename any mp3 to this name to test it:

    
#include <iostream>
#include <Files.h>
#include <CFBase.h>
void	printCFStringRef(CFStringRef*);

OSErr FilePathToFSSpec(char *path, FSSpec *spec) {

	OSErr err = noErr;
	CFStringRef cfPath = CFStringCreateWithCString(kCFAllocatorDefault,
							path, kCFStringEncodingUTF8);

	if(!cfPath) { printf("Failed on CFStringCreateWithCString\n"); return -1; }

	printf("CFStringCreateWithCString produced: ");
	printCFStringRef(&cfPath);
	printf("\n");

	CFURLRef cfURL = CFURLCreateWithFileSystemPath(kCFAllocatorDefault,
						cfPath, kCFURLPOSIXPathStyle, false);

	if(!cfURL) { printf("Failed on CFURLCreateWithFileSystemPath\n"); return -1; }

	FSRef fileRef;
	if (CFURLGetFSRef(cfURL, &fileRef)) {
		err = FSGetCatalogInfo(&fileRef, kFSCatInfoNone, NULL, NULL, spec, NULL);
	} else {
		printf("Failed on CFURLGetFSRef\n");
		CFRelease(cfURL);
		return -1;
	}

	CFRelease(cfURL);
	CFRelease(cfPath);

	if(err) printf("Failed on CFURLCreateWithFileSystemPath err is %d\n",err);
	else {
		int lnmSize = spec->name[0];
		char buffer[256];
		sprintf(buffer,"StrFileName is {\"%%.%ds\"}\n",lnmSize);
		printf(buffer,&spec->name[1]);
	}
	
	return err;
}

void	printCFStringRef(CFStringRef* str) {
	int length = CFStringGetLength(*str);
	int dataSize = sizeof(UniChar)*(length+1);
	char* dataPtr = (char*)NewPtrClear(dataSize);
	if (nil != dataPtr)
	{
		if (!CFStringGetCString(*str,dataPtr,dataSize,kCFStringEncodingASCII))
		{
			if (!CFStringGetCString(*str,dataPtr,dataSize,kCFStringEncodingUTF8))
			{
				DisposePtr(dataPtr);
				dataPtr = NULL;
				length = dataSize = 0;
			}
		}
	}
	if (length > 0)
		printf("{\"%s\"}",dataPtr);
	else
		printf("\"<none!>\"}");
	if (nil != dataPtr)
		DisposePtr(dataPtr);
}

int main (int argc, char * const argv[]) {
	if(argc<2) return 0;
	char *lname = argv[1];
	printf("The original filename passed is {\"%s\"}\n",lname);
	FSSpec lSpec;
	OSErr lErr = FilePathToFSSpec(lname, &lSpec);
	return 0;
}


Attached is a console session using the program,  The first two attempts to run the program failed.  They used absolute and relative paths to the file on the Nfs hosted folder (~/mnt/mkl9).  The next two attempts succeeded to access the same file on a Mac hosted folder (~/mnt/mylt) and in the local fine system (~):

    
/Users/mikef/mnt/mylt !>NFS-path-test ../../mk*/Q*
The original filename passed is {"../../mk*/Q*"}
CFStringCreateWithCString produced: {"../../mk*/Q*"}
Failed on CFURLGetFSRef


/Users/mikef/mnt/mylt !>NFS-path-test /Users/mikef/mnt/mkl9/Q*
The original filename passed is {"/Users/mikef/mnt/mkl9/Queensr?che-Empire-01-Best I Can.mp3"}
Failed on CFStringCreateWithCString


/Users/mikef/mnt/mylt !>NFS-path-test Q*                      
The original filename passed is {"QueensrÃ¿che-Empire-01-Best I Can.mp3"}
CFStringCreateWithCString produced: {"QueensrÃ¿che-Empire-01-Best I Can.mp3"}
StrFileName is {"Queensr??che-Empire-01-B#14.mp3"}


/Users/mikef/mnt/mylt !>NFS-path-test ~/Q*
The original filename passed is {"/Users/mikef/QueensrÃ¿che-Empire-01-Best I Can.mp3"}
CFStringCreateWithCString produced: {"/Users/mikef/QueensrÃ¿che-Empire-01-Best I Can.mp3"}
StrFileName is {"Queensr??che-Empire-0#A6C3D.mp3"}
/Users/mikef/mnt/mylt !>


----
The fundamental problem here is that Cocoa and CoreFoundation assume that all paths are UTF-8 encoded. This is generally true for local disks, but not necessarily the case when connecting to a remote machine using NFS on a different operating system. Most UNIX-like operating systems simply assume that paths are just char * with no specific encoding, so any sequence of non-zero bytes is a valid path.

As you discovered, it is possible to guess at the encoding and get it into a CF/NSString that way, but this string will not produce the right sequence of bytes when decoded by code which doesn't know about the special encoding. This is a pretty tough situation and you may not have good choices available, but there are some things you may be able to do:


* An obvious one would be to get the Linux machine to use UTF-8 in filenames. How difficult this is will probably depend on what software is generating these files, or how easy it is to convert them in place.
* Don't use NS/CFString to manipulate these filenames. Keep them all as char *. If you need to store them in Cocoa containers, use NSData. Only use APIs which take char * when manipulating the files.
* Write a custom NSString subclass with a -fileSystemRepresentation which doesn't return UTF-8. This one is a bit scary and you may need to override more methods depending on what the framework code does with your objects, but it could be a way to get non-UTF-8 paths to work with Cocoa and CoreFoundation.


----
You could also look into sharing over SMB instead, but you still have to remember that MacOSX uses decomposed characters (the codepoints for combining diacritics are stored separately from the base character), whereas Windows and Linux use precomposed UTF-8 (where possible, a codepoint refering to a diacritic-character combo is used).  Take a look at this T<nowiki/>echNote: http://developer.apple.com/qa/qa2001/qa1173.html
----
Thanks,

I�ll look into forcing the Linux machine to use UTF-8 filenames and into SMB.  The bug was first reported by someone using our code, but giving them these options is better then telling them to change all their file names. I spent the day before I wrote this question looking at avoiding NS/CFS<nowiki/>tring, or finding some other path using different calls then I am using, but couldn�t find a solution.  I am new to Cocoa, and new to the code base I am working on, so I�d like to carve as little a whole in the code base as I can.  If you know of a way of getting from �char* filename� to O<nowiki/>penMovieFile(FFS<nowiki/>pec*,short*,SI<nowiki/>nt8) or to something that does the same thing as O<nowiki/>penMovieFile, or if you could point me in the right direction, that would be appreciated. In any case I�ll look into both these options and let you know what I come up with.  The third option I think I will leave alone for now, because as you say �This one is a bit scary�.

MikeFusco 

----
A possible workaround could be to use C and unix-isms to create a temporary symbolic link (with any easy to use filename) to the file, and then open the file via the link.

----
Whoever wrote this suggestion is, at least temporarily, a genius. Nice job of thinking outside the box!

