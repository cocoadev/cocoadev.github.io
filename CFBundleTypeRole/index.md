---
layout: page
title: CFBundleTypeRole
---

CFBundleTypeRole is a document type property within a dictionary in a CFBundleDocumentTypes array in Info.plist.

It is mainly used on the Mac; it may be unused on iOS, where it isn't mentioned in any of that platform's Programming Guides.

== Known role values ==

*None
*Viewer: The application can open, but not save, documents of this type. The document-based app machinery will disable the Save command(s).
*Editor: The application can open and save documents of this type.
*Shell
*QLGenerator: For Quick Look Generator plug-ins.
*MDImporter: For Spotlight Importer plug-ins.

----
*original classic-CocoaDev discussion follows*

For a long time I've been confused about what the "CFBundleTypeRole" key in Info.plist is for. I recently found out, and thought I'd share this with you folks here. I've also posted this info to the MacOS X Guru Site [3].

In the Info.plist file, you can set up a CFBundleTypeRole for each document type, which can be either "Viewer" or "Editor". You can use these in Cocoa to allow importing/exporting various kinds of files, as well as implement template files ("stationeries") without having to write much code.

If you specify the "Viewer" role for your document type, it means that your application can open and read a particular file format, but can not save in this file format. Furthermore, Cocoa will open files of this type as "untitled" documents.

"Editor", on the other hand, means that your application can read, edit and write files of a particular type, and will give you the usual document behavior.

Now, what if your application can read a particular file format and export to it, but it is a lossy conversion? The user is not technically editing a file of this type. What you do is that you declare this file format's role as "viewer", and then add a NSExportableAs key to another document type. This key contains an array of strings with the names of other file formats to which this document type can be exported.

In addition, you add a "Save To..." menu item to your "File" menu (though I chose to rename it to "Export...", as "save to..." sounds too much like "Save as..." to me, and Apple seems to agree on the latter, if not on the former - see [1] ), which will take care of calling dataRepresentationOfType: or a similar call to actually cause the export. All you have to do is look at the type parameter to find out what file format you're supposed to save to.

It works the same way for loadDataRepresentation:ofType: and importing "viewer-only" files. You can specify the same document class as you use for your native documents here, and just do an if( [type isEqualToString: @"foo"] ) to find out what file format you're expected to read.

You can even use this to implement stationeries, by making stationeries a read-only type and then doing a setType: to change their type to a different type that your app can save to. (You will need to do this in readFrom... method *after* calling the inherited method, or it won't stick)

Most of this info was gleaned from the NSDocument FAQ [2]

-- UliKusterer


[1]: http://developer.apple.com/documentation/LegacyTechnologies/Conceptual/AquaHIGuidelines/AHIGMenus/
chapter_4_section_4.html#//apple_ref/doc/uid/20000960/TPXREF105

[2]: http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Documents/Tasks/FAQ.html

[3]: http://www.macosxguru.net

----

It looks like the 'Viewer' doesn't automatically untitle the document. I also couldn't find any official docs on this behaviour. Instead, I did this:

    
- (BOOL)readFromData:(NSData *)data ofType:(NSString *)typeName error:(NSError **)outError
{
	*outError = nil;
	if ([typeName isEqualToString:@"SomeReadOnlyType"])
	{
		// .. (load data here)
		**[self setFileURL:nil];**
		
		return result;
	}
	else
	{
		// .. (do whatever you do for other documents here)
	}
}


Setting the fileURL to nil will cause the document to be considered new and untitled again.

�Sijmen

