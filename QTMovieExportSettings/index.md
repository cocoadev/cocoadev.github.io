---
layout: page
title: QTMovieExportSettings
---



    QTMovieExportSettings is a key used with QTKit's     -[QTMovie writeToFile:withAttributes:] method. It allows full control over the type of file that is written, using QuickTime's standard export settings dialogs. Unfortunately, it's a bit underdocumented. The purpose of this page is to demonstrate how to use it to export movies. Be warned, QTKit doesn't cover everything that's needed, so we have to drop back to the old C interfaces in a lot of places.

The code on this page is mostly taken from a working example that sits on my hard drive, with a few small modifications that have not been tested. http://goo.gl/OeSCu

----

Our first task is to get a list of available export components in a Cocoa-friendly form. To do this, we repeatedly call     FindNextComponent followed by     GetComponentInfo and store the results in an array of NSDictionaries. Here is the code:

    
- (NSArray *)availableComponents
{
	NSMutableArray *array = [NSMutableArray array];
	
	ComponentDescription cd;
	Component c = NULL;
	
	cd.componentType = MovieExportType;
	cd.componentSubType = 0;
	cd.componentManufacturer = 0;
	cd.componentFlags = canMovieExportFiles;
	cd.componentFlagsMask = canMovieExportFiles;
	
	while((c = FindNextComponent(c, &cd)))
	{
		Handle name = NewHandle(4);
		ComponentDescription exportCD;
		
		if (GetComponentInfo(c, &exportCD, name, nil, nil) == noErr)
		{
			unsigned char *namePStr = *name;
			NSString *nameStr = [[NSString alloc] initWithBytes:&namePStr[1] length:namePStr[0] encoding:NSUTF8StringEncoding];
			
			NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:
				nameStr, @"name",
				[NSData dataWithBytes:&c length:sizeof(c)], @"component",
				[NSNumber numberWithLong:exportCD.componentType], @"type",
				[NSNumber numberWithLong:exportCD.componentSubType], @"subtype",
				[NSNumber numberWithLong:exportCD.componentManufacturer], @"manufacturer",
				nil];
			[array addObject:dictionary];
			[nameStr release];
		}
		
		DisposeHandle(name);
	}
	return array;
}


I'm not at all sure that UTF-8 is the correct encoding to use for the name, but I couldn't find any information about it. In any case, all of the components on my system are plain ASCII.

Benoit : Encoding should be NSMacOSRomanStringEncoding instead.

If you were allowing the user to choose the component, then you could display a list of the     name keys. The     type key will always contain the four-char code     'spit' because these are export components, but it's included for completeness. The     subtype key contains a juicy bit, it specifies the media type for this particular component, which we have to give in our attributes dictionary later on. The     manufacturer key is also critical, as this allows QuickTime to distinguish between two different components which output the same type.

On my machine, this code returns two AIFF exporters, one which is apparently intended for MIDI files and one which is intended for regular sound files. I'm not sure what the difference is, or how to only show one without simply hardcoding the data to ignore.

----

The Component c wasn't initialized to NULL. I've fixed that, because otherwhise your component search may be starting at an arbitrary component in the list. -- UliKusterer

----

Once a component is selected, we want to display the QuickTime export settings for this component. This is the phase that gets us the data to use with the     QTMovieExportSettings key. For this code, we will get the     Component out of our info dictionary, use     OpenComponent to open it, then call     MovieExportDoUserDialog to run the export settings dialog. Once that completes, we use     MovieExportGetSettingsAsAtomContainer to get a     QTAtomContainer which describes the settings chosen. Finally we wrap it up in an NSData.

    
- (NSData *)getExportSettings
{
	Component c;
	memcpy(&c, [[self availableComponents] objectAtIndex:selectedComponentIndex] objectForKey:@"component"] bytes], sizeof(c));
	
	[[MovieExportComponent exporter = OpenComponent(c);
	Boolean canceled;
	ComponentResult err = MovieExportDoUserDialog(exporter, NULL, NULL, 0, 0, &canceled);
	if(err)
	{
		NSLog(@"Got error %d when calling MovieExportDoUserDialog",err);
		CloseComponent(exporter);
		return nil;
	}
	if(canceled)
	{
		CloseComponent(exporter);
		return nil;
	}
	QTAtomContainer settings;
	err = MovieExportGetSettingsAsAtomContainer(exporter, &settings);
	if(err)
	{
		NSLog(@"Got error %d when calling MovieExportGetSettingsAsAtomContainer",err);
		CloseComponent(exporter);
		return nil;
	}
	NSData *data = [NSData dataWithBytes:*settings length:GetHandleSize(settings)];
	DisposeHandle(settings);

	CloseComponent(exporter);
	
	return data;
}


If you've followed this far, congratulations! The hard bits are all over.

----

The NSLog statements were missing the error number parameter (thus always printing 0, if you're lucky), I added that. -- UliKusterer

----

For the last step, we want to write the file to disk. Of course, QTKit makes this part easy for us! All we have to do is build an attributes dictionary using the information we already collected, then make a simple call, and it's done.

    
- (BOOL)writeMovie:(QTMovie *)movie toFile:(NSString *)file withComponent:(NSDictionary *)component withExportSettings:(NSData *)exportSettings
{
	NSDictionary *attributes = [NSDictionary dictionaryWithObjectsAndKeys:
		[NSNumber numberWithBool:YES], QTMovieExport,
		[component objectForKey:@"subtype"], QTMovieExportType,
		[component objectForKey:@"manufacturer"], QTMovieExportManufacturer,
		exportSettings, QTMovieExportSettings,
		// do not set the QTMovieFlatten flag! (causes export settings to be ignored)
		nil];
	
	BOOL result = [movie writeToFile:file withAttributes:attributes];
	if(!result)
	{
		NSLog(@"Couldn't write movie to file");
		return NO;
	}
	
	return YES;
}


The comment regarding QTMovieFlatten during the setup of 'attributes' is in regards to anyone who has built their movie from individually produced/loaded image frames, and might think you need QTMovieFlatten as seen in Apple's example code for that operation.  I believe since we are specifying export settings here, that implies the flatten.  If you explicitly specify flatten, it ignores the compression settings and produces a (probably quite large) movie of "individual" frames. (i.e. not compressed together with the chosen codec)

Actually getting a QTMovie to export is simple using     +[QTMovie movieWithFile:error:], and the code above demonstrates how to get the component and export settings this method needs, and that's it!

Please feel free to correct all of the mistakes I've undoubtedly made on this page. -- MikeAsh

----

First of all, I also get two AIFF encoders, so that should not be unusual.  Be careful if entering the names into a NSPopUpButton for a save dialog, because it will replace items with the same name, so your menu indicies will wind up not matching the availableComponent indicies, and confusion will result.  Instead, try something like this, which will append a '-N' counter (e.g. AIFF, AIFF-2) to components with the same name:
    
	int selectedComponentIndex=-1; // declare as a class member if you want to remember the user's choice
	IBOutlet NSPopUpButton * encodingPopUpButton;
	
	//from your NSSavePanel setup, e.g. NSDocument's prepareSavePanel
	[encodingPopUpButton removeAllItems];
	NSArray * encoders = [self availableComponents];
	NSEnumerator *enumerator = [encoders objectEnumerator];
	while (NSDictionary* dict = [enumerator nextObject]) {
		NSString * name=[dict objectForKey:@"name"];
		for(unsigned int i=2; [encodingPopUpButton itemWithTitle:name]!=nil; ++i)
			name=[NSString stringWithFormat:@"%@-%u",[dict objectForKey:@"name"],i];
		// next two lines cause default to "QuickTime Movie" if nothing else has been chosen yet
		if(selectedComponentIndex==-1 && [name isEqualToString:@"QuickTime Movie"])
			selectedComponentIndex=[encodingPopUpButton numberOfItems];
		[encodingPopUpButton addItemWithTitle:name];
	}
	if(selectedComponentIndex!=-1)
		[encodingPopUpButton selectItemAtIndex:selectedComponentIndex];


The only thing I noticed, was at the MovieExportDoUserDialog. Some of the encoders provide the information, how large the encoded file will be, with the actual settings. To do this, you must give the function a     Movie reference. Second  (it does not seem to be necessary if you want to export the whole movie, though) you should provide the function with the time, your exported movie shall have out of the old movie.
Note:     aMovie here is an instance of     QTMovie.

    
Movie theMovie = [aMovie quickTimeMovie] ;
TimeValue duration = GetMovieDuration(theMovie) ;

ComponentResult err = MovieExportDoUserDialog(exporter, theMovie, NULL, 0, duration, &canceled) ;


LorenzHipp

----

Could anyone explain how you can open the dialog with settings already chosen before? Like how QuickTime Pro would have already settings for MPEG-4 with settings such as:

http://homepage.mac.com/meddiecatsoft/mpeg4settings.jpg

How can I use the data collected from a previous dialog and open up those settings just like QuickTime does?

*I haven't tested it, but calling     MovieExportSetSettingsFromAtomContainer() before running the actual dialog might do it.*

----

>*I haven't tested it, but calling     MovieExportSetSettingsFromAtomContainer() before running the actual dialog might do it.*

It's correct. I use this method.

Moments Studio

----

For some components (e.g. the DivX Pro Codec 6.2) MovieExportDoUserDialog returns a value which evaluates to true if they are canceled. So you should test _first_ if the dialog was canceled and _then_ if an error occured.

-- Stefan Heukamp

----

Question 22/08/2006:
Is there a way to display a progress bar to the user while the export is taking place? There appears to be no Notifications sent by the exporting routine.
-- Drarok

----

    
- (BOOL)movie:(QTMovie *)movie shouldContinueOperation:(NSString *)op withPhase:(QTMovieOperationPhase)phase atPercent:(NSNumber *)percent withAttributes:(NSDictionary *)attributes;


----

Here is a new version of the 'availableComponents' method from above.
The OSType fields are strings and the "component" field is now an NSValue.
This makes the output easier to read if your using it for debugging purposes.


    

- (NSArray *)availableComponents
{
	NSMutableArray		*results = nil;
	ComponentDescription	cd = {};
	Component		 c = NULL;
	Handle			 nameHandle = NewHandle(0);
	
	if ( nameHandle == NULL )
		return( nil );
	
	cd.componentType = MovieExportType;
	cd.componentSubType = 0;
	cd.componentManufacturer = 0;
	cd.componentFlags = canMovieExportFiles;
	cd.componentFlagsMask = canMovieExportFiles;

	while((c = FindNextComponent(c, &cd)))
	{
		ComponentDescription	exportCD = {};
		
		if ( GetComponentInfo( c, &exportCD, nameHandle, NULL, NULL ) == noErr )
		{
			HLock( nameHandle );
			NSString	*nameStr = [[[NSString alloc] initWithBytes:(*nameHandle)+1 length:(int)**nameHandle encoding:NSMacOSRomanStringEncoding] autorelease];
			HUnlock( nameHandle );
			
			exportCD.componentType = CFSwapInt32HostToBig(exportCD.componentType);
			exportCD.componentSubType = CFSwapInt32HostToBig(exportCD.componentSubType);
			exportCD.componentManufacturer = CFSwapInt32HostToBig(exportCD.componentManufacturer);
				
			NSString *type = [[[NSString alloc] initWithBytes:&exportCD.componentType length:sizeof(OSType) encoding:NSMacOSRomanStringEncoding] autorelease];
			NSString *subType = [[[NSString alloc] initWithBytes:&exportCD.componentSubType length:sizeof(OSType) encoding:NSMacOSRomanStringEncoding] autorelease];
			NSString *manufacturer = [[[NSString alloc] initWithBytes:&exportCD.componentManufacturer length:sizeof(OSType) encoding:NSMacOSRomanStringEncoding] autorelease];
			
			NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:
				nameStr, @"name", [NSValue valueWithPointer:c], @"component",
				type, @"type", subType, @"subtype", manufacturer, @"manufacturer", nil];
			
			if ( results == nil ) {
				results = [NSMutableArray array];
			}
			
			[results addObject:dictionary];
		}
	}
	
	DisposeHandle( nameHandle );
	
	return results;
}



--rjt

----

I had some problems with the new version of "availableComponents" above in combination with Cocoa Bindings. I fixed it by using a NSData instead of the NSValue. 

--Martin Kahr

----

A note to all you QTKit dabblers: QTMovie<nowiki/>'s     -writeToFile:... is synchronous, which doesn't really mesh with the idea of a responsive NSRunLoop. You can run it in another thread, but you must first detach the movie from the main thread and the component that handles the movie must support threading (which is not true in some notable cases, like .movs using older codecs or some files when Perian is involved). The best solution would be running a satellite helper tool that actually does the exporting.

(Also note that this is true for 32-bit apps only. 10.5 64-bit apps already spawn an helper tool to do the exporting.) -- EmanueleVulcano aka l0ne

----
A simpler alternative is to simply call     runModalSession: in the progress callback to keep a modal progress dialog updated. You still block the application but you avoid the beachball and can show the progress of the export. This is what I do in QTAmateur and it's a reasonable approach. It's nicer to have exports be non-modal but much more effort to do so. -- MikeAsh

----

When I tried this I got some compile errors saying selectedComponentIndex is not declared. You don't mention what that is or how you determine what it's value is.
Also the method:
- (BOOL)writeMovie:(QTMovie *)movie toFile:(NSString *)file withComponent:(NSDictionary *)component withExportSettings:(NSData *)exportSettings
you don't mention what component is or how you got it..
can you tell me what I need to do to get that to work, please?

----

selectedComponentIndex is the index of an element in the array returned by availableComponents. You can let the user select this by populating a menu with the array or you can just pass an index. Likewise, component is an element of the array returned by availableComponents.

----
Is there any chance to get this Code as XCode Project? Compile didn't work for me... --Andi

----
Any chance that there is one web page in 2011 that tells how to save an audio file from Cocoa?

