---
layout: page
title: NSOpenPanelSupportedFileTypesFromInfoPropertyList
---

NSOpenPanel allows you to define a list of "supported" file types through the runModalForDirectory:file:types: method. This accepts an array of strings containing supported file extensions.

While it's possible to define a list in the code, it's also much more manageable to simply generate this array from the list of supported file types (and exported file types) in your Info.plist file. The code below demonstrates how to do this:

    
	NSMutableSet *supportedFileTypesSet = [NSMutableSet set];

	// Grab the "supported" file types
	NSArray *extensionFileTypes = [[[NSBundle mainBundle] infoDictionary] valueForKeyPath:@"CFBundleDocumentTypes.@distinctUnionOfArrays.CFBundleTypeExtensions"];
       [supportedFileTypesSet addObjectsFromArray:extensionFileTypes];

	// Grab the "exported" file types
	NSArray *exportedFileTypesArray = [[[[NSBundle mainBundle] infoDictionary] valueForKeyPath:@"UTExportedTypeDeclarations.UTTypeTagSpecification"] valueForKey:@"public.filename-extension"]; // "public.filename-extension" key can't be used with valueForKeyPath:

       NSEnumerator *exportedFileTypesArrayEnumerator = [exportedFileTypesArray objectEnumerator];
       NSArray *_currentFileType = nil;

       while ((_currentFileType = [exportedFileTypesArrayEnumerator nextObject]))
       {
              [supportedFileTypesSet addObjectsFromArray:_currentFileType];
       }

       // Pop the unique file extensions into an array
	NSArray *supportedFileTypesArray = [supportedFileTypesSet allObjects];

       // Initiate the open panel...
	int result = [oPanel runModalForDirectory:nil file:nil types:supportedFileTypesArray];


-- JeremyHiggs

----
It is probably worth noting that if you use an NSDocument-based app then the framework does all of this for you and you write zero lines of code.

