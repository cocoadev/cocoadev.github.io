---
layout: page
title: HTMLFromRTFD
---



Hi. Trying to make an existing app save rtfd files as html (*not* a webarchive). I managed to get it to more or less work using code like this:

    
NSString *docType = NSHTMLTextDocumentType;
NSDictionary *attributes = [NSDictionary dictionaryWithObject: docType forKey:NSDocumentTypeDocumentAttribute];
NSFileWrapper *wrapper = [textStorage fileWrapperFromRange: range documentAttributes: attributes error: NULL];


Unfortunately this ends up saving files in a directory call M<nowiki/>yFileName.htm (which is a little ugly), containing files index.html, and various attachments like image files. I need to have the 'index' file named M<nowiki/>yFileName.htm, and the attachments in the same directory, ideally the enclosing directory of the path passed into writeToFile:ofType: where all this is happening.

I tried a bit of code like the following which takes the file wrapper created, gets the contained wrappers, modifies and writes them. 

    
if ([textStorage containsAttachments]) {
	// get a directory wrapper which holds the html and attachments
	NSFileWrapper *wrapper = [textStorage fileWrapperFromRange: range documentAttributes: attributes error: NULL];
	success = wrapper ? YES : NO;
	// if no wrapper bail out...
	if(!success) return success;
	// now we have to enumerate over the various wrappers, fixing and renaming the html, and saving attachments
	NSEnumerator *files = wrapper fileWrappers] objectEnumerator];
	[[NSFileWrapper *thisFile;		
	while(thisFile = [files nextObject]) {
		if(thisFile preferredFilename] isEqualToString: @"index.html"]) {
			// maybe should check here that this is a regular file?
			// get the contents as a string, and remove "file:" [[URLs in links so as to have relative URLs
			NSMutableString *html = [[[NSMutableString alloc] initWithData: [thisFile regularFileContents] encoding: NSUTF8StringEncoding] autorelease];
			[html replaceOccurrencesOfString: @"file:///" withString: @"" options: NSCaseInsensitiveSearch range: NSMakeRange(0, [html length])];
			// this file needs to be findable by name, so we use path directly rather than index.html
			success = success && [html writeToFile: path atomically: YES];
		} else {
			// these are attachments
			NSString *thisPath = path stringByDeletingLastPathComponent] stringByAppendingPathComponent: [thisFile preferredFilename;
			success = success && [thisFile writeToFile: thisPath atomically:YES updateFilenames: YES];
		}
	}
... return success;


Unfortunately, the path passed into w<nowiki/>riteToFile:OfType: is not the one requested in the save dialog, it's something in a temp directory. I assume this is to allow for things to be written atomically, and the written file is moved to the requested location after saving somewhere else in the NSDocument implementation. This means the attachments aren't moved, just the 'index' file.

Any suggestions for how best to make this work?

