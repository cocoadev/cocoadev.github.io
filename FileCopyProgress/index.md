---
layout: page
title: FileCopyProgress
---



Is there a simple clean way to get the progress (ie. bytes copied) of a file copy that is occuring in the background?

I am looking for some sort of Cocoa wrapper or a way to do it via NSFileManager.

----

*bump* is there really nothing like this??

----

You would probably have to do it using some Carbon call; You may also be able to have another thread using NSFileManager to get the size of the destiantion file during copying, but I would say that that would be verrrrry buggy, and hard to implement in realtime.

----

I don't think that there would be a way to do this since the actual file copy may be done in an atomic way in some part of the kernel or subsystem (I can't remember if there is a *copy file* syscall but it would otherwise be a zero-copy, I would think).  If you look at the Finder, for example, it doesn't seem to know how far along one file is but only how many files have been copied.

If you really need this, you could just manually copy the file by opening the descriptors and reading and writing some fixed-sized buffer between them.  That way you would always know how far it was since you would know how many payloads of the total were copied.

----

I like the last idea, just above... about copying part by part manually... I want to create an NSFileManager category for this. Can anyone help out with a simple hint on how I would go about splitting any given file, could be small, could be large into blocks and copying it all reliably part by part?

Okay I've got it working:

    
- (BOOL)copyPath:(NSString *)source toPath:(NSString *)destination delegate:(id)delegate {
	BOOL isDirectory;
	if(![self fileExistsAtPath:source isDirectory:&isDirectory] || [self fileExistsAtPath:destination]) return NO;
	else if(isDirectory) return [self copyPath:source toPath:destination handler:delegate];

	FILE				*   sourceFile;
	FILE				*   destinationFile;
	char					buffer[8192];
	unsigned long long		bytes;
	
	if(!(sourceFile = fopen([source fileSystemRepresentation], "r"))) {
		return NO;
	} else if(!(destinationFile = fopen([destination fileSystemRepresentation], "w+"))) {
		NSDictionary * errorDict = [NSDictionary dictionaryWithObjectsAndKeys:
										@"Couldn't create", @"Error",
										destination, @"Path",
									nil];
		[delegate performSelector:@selector(fileManager:shouldProceedAfterError:) withObject:self withObject:errorDict];

		fclose(sourceFile);
		return NO;
	}

	while(bytes = fread(buffer, 1, sizeof(buffer), sourceFile)) {
		if(!fwrite(buffer, 1, bytes, destinationFile)) {
			NSDictionary * errorDict = [NSDictionary dictionaryWithObjectsAndKeys:
											@"Couldn't create", @"Error",
											destination, @"Path",
										nil];
			[delegate performSelector:@selector(fileManager:shouldProceedAfterError:) withObject:self withObject:errorDict];
			break;
		}
		if([delegate performSelector:@selector(copiedBytes:toPath:) withObject:[NSNumber numberWithUnsignedLongLong:bytes] withObject:destination]) break;
	}
	
	fclose(destinationFile);
	fclose(sourceFile);
	
	return YES;
}


Of course you'd want this in a thread if it is to be of much use. You can cancel the copy by returning a YES from the delegate. 

Can someone look over the code to see if I've missed anything?

----

Getting more advanced, does anyone know how to stop OS X from caching the file during a copy, ie. so that the progress is accurate, as this is going on a removable device... or a way to flush the filesystem buffer?

----
On the original question...

How about using NSFileHandle readInBackgroundAndNotify on the input file? It should get you where you want with very little effort.
----

The code above doesn't handle resource forks or other metadata. NSFileManager does (as of 10.2). Also take a look at the FSCopyObject sample code from DTS. -- FinlayDobbie

----

Why not just periodically check the size of the destination file against the size of the source file, and set the progress bar accordingly? This should take care of accuracy, forks, etc., while still letting the OS do its thing. �DustinVoss

--

Well, luckily in my case, this is exactly what I want, *NO* resource forks :)

----

You can't do the size check on the destination file - you're ignoring caching. And the copy method posted above will probably work, but should use some bigger buffer (and not copy 1 byte at a time) - the FSCopyObject sample referenced above does that. --Cristi

