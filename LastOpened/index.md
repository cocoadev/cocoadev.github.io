---
layout: page
title: LastOpened
---



Hey all,

Trying to find out the last opened attribute of a file (the same one displayed in the Finder inspector).  I've got this code here:

    NSDate *openDate = [NSDate date];
OSStatus err;
FSRef fileRef;
err = FSPathMakeRef([path fileSystemRepresentation], &fileRef, NULL);
if (err == noErr) {
	FSCatalogInfo catalogInfo;
	err = FSGetCatalogInfo(&fileRef, kFSCatInfoAccessDate, &catalogInfo, NULL, NULL, NULL);
	if (err == noErr) {
		openDate = UTCDateTimeToNSDate(&catalogInfo.accessDate);
	}
}

Now, when I use that similar method to obtain the modification or creation date -- it works fine.  With the accessDate, however, I am given something inaccurate.  it simply returns todays date.  Does anyone have any idea how to make this work and get the real access/last opened date of a file?  Thanks

----
Well, your default value for openDate is the current date. Perhaps you should check if FSPathMakeRef and FSGetCatalogInfo are, in fact, returning noErr? If one of them isn't, then the value of openDate remains the current date (minus the time it took to execute your code, that is.) Also, is it possible that either FSGetCatalogInfo or FSPathMakeRef update the access date of the files they represent when one uses them? -JonathanGrynspan

---- 

Thanks for the reply Jonathan.  I have checked that the code was being executed up to the point where it is set to the accessDate (and it is).  I thought that, perhaps, it was updating the access date but it does not change the finder info view of the Last Opened attribute of the file -- so either this is a different attribute it is reading from, or this is just not reading it properly.

