---
layout: page
title: MFourPTagExtraction
---

I am trying to extract the metadata tags (name, artist, album, etc) from an M4P file using QuickTimes carbon UserData functions. However unlike MP3's which return a nice bunch of tags it M4P's return a massive blob of data with no obvious use,. Does anyone have any idea what this blob of data is and how to interpret it? The code i am using is as follows

    
- (void)getUserData:(UserData)userData
{
	Handle handle;
	OSType type;
	char characters = nil;
	short i, count;
	OSErr error = noErr;
	
	handle = NewHandle(0);
	type = GetNextUserDataType(userData, 0);
	
	if(0 != type)
	{
		printf("\nMeta-data:\n");
		while (0 != type)
		{
			count = CountUserDataType(userData, type);
			for(i = 1; i <= count; i++)
			{
				if((type >> 24) == kUserDataIsText)
				{
					error = GetUserDataText(userData, handle, type, i, langEnglish);
					if(error)
					{
						NSLog(@"Error ... %i", error);
					}
					else
					{
						PtrAndHand(&characters, handle, 1);
						
						HLock(handle);
						NSString *name = [NSString stringWithFormat:@"%c%c%c%c", 
							(char)(type >> 24), 
							(char)(type >> 16), 
							(char)(type >> 8), 
							(char)type];
						NSString *content = [NSString stringWithFormat:@"%s", *handle];
						NSLog(@"%@: %@", name, content);
						HUnlock(handle);
					}
				}
				else
				{
					
					error = GetUserData(userData, handle, type, i);
					if(error)
					{
						NSLog(@"Error ... %i", error);
					}
					else
					{
						NSString *name = [NSString stringWithFormat:@"%c%c%c%c", 
							(char)(type >> 24), 
							(char)(type >> 16), 
							(char)(type >> 8), 
							(char)type];
						NSData *bytes = [NSData dataWithBytes:(const void *)handle length:GetHandleSize(handle)];
						NSLog(@"%@: %@", name, bytes);
					}
				}
			}
			type = GetNextUserDataType(userData, type);
		}
	}
	DisposeHandle(handle);
}


Thanks in advance.

Peter

----

If you're developing for 10.4+, try using the Spotlight APIs to get this info.

----

Note that Spotlight won't work for files that aren't indexed, such as files on remote drives, or anything in an exclusion directory.

----

I don't know the answer but I spotted one error in your code, which may have some bearing on the "blob of data" effect.     [NSData dataWithBytes:(const void *)handle length:GetHandleSize(handle)];        - dataWithBytes: expects a pointer to some data. A handle is a *pointer to a pointer* to some data, so you have to dereference it at least once. So I suspect this should be     [NSData dataWithBytes:(const void *) *handle length:GetHandleSize(handle)];. At present your NSData will contain bytes copied from the Carbon-style memory manager master pointer list, which is probably not what you wanted. In OSX, these tables are still maintained for compatibility - i.e. handles are still handles, and don't become ordinary pointers on OSX. Also, not that it matters, but HLock and HUnlock do nothing on OSX, so you don't need them (and this code can't run on <OSX because it contains Cocoa code). However they are harmless. --GrahamCox

